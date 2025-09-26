# 第20章：優先次序（Prioritization）

在複雜而動態的環境中，代理（Agent）經常面對眾多潛在行動、互相衝突的目標以及有限的資源。若沒有一套明確的流程去決定下一步行動，代理（Agent）可能會出現效率下降、營運延誤，甚至無法達成關鍵目標。優先次序設計模式（Prioritization pattern）透過讓代理（Agent）根據重要性、緊急程度、相依性以及既定準則來評估及排序任務、目標或行動，從而解決這個問題。如此便能確保代理（Agent）將精力集中於最關鍵的工作，提升效益並與目標保持一致。

## 優先次序設計模式（Prioritization Pattern）概覽

代理（Agent）運用優先次序設定來有效管理任務、目標及子目標，並引導下一步行動。此過程有助於在面對多重需求時作出知情決策，將至關重要或緊急的活動置於次要事項之前。這在資源受限、時間緊迫、目標可能衝突的真實情境中特別重要。

代理（Agent）進行優先排序的基本面向通常包含幾個元素。首先是準則定義，為任務評估建立規則或指標，當中可包括緊急性（任務的時間敏感度）、重要性（對主要目標的影響）、相依性（該任務是否為其他工作的先決條件）、資源可用性（所需工具或資訊是否齊備）、成本／效益分析（投入與預期成果的比較），以及針對個人化代理（Personalized Agent）的使用者偏好。其次是任務評估，透過由簡單規則至大型語言模型（Large Language Model，LLM）的複雜評分或推理方法，按既定準則評估每個潛在任務。第三是排程或選擇邏輯，即根據評估結果挑選最佳下一步行動或任務序列的演算法，可能使用佇列或進階規劃元件。最後是動態再優先排序，讓代理（Agent）在情況改變時調整優先次序，例如出現新的緊急事件或截止日期逼近，確保代理（Agent）具備適應性與回應能力。

優先排序可發生於不同層次：挑選高層目標（高階目標優先排序）、安排計劃中的步驟（子任務優先排序），或從可行選項中挑選下一個即時行動（行動選擇）。有效的優先排序能讓代理（Agent）在複雜、多重目標的環境中展現更智能、高效及穩健的行為。這與人類團隊的組織方式相似，管理者會綜合各成員的意見來決定任務的先後次序。

## 實務應用與使用案例

在各種真實應用中，人工智慧代理（AI Agent）透過精密的優先排序來作出及時而有效的決策。

* **自動化客戶支援**：代理（Agent）會將系統中斷等緊急請求置於密碼重設等例行事務之前，並可能優先處理高價值客戶。
* **雲端運算**：人工智慧（AI）在高峰期會把資源優先分配給關鍵應用程式，並將不太緊急的批次工作安排於離峰時間，以優化成本。
* **自動駕駛系統**：持續設定行動優先次序以確保安全與效率。例如，為避免碰撞而煞車會優先於維持行車線或提升燃油效率。
* **金融交易**：交易機械人（Trading Bot）透過分析市場情況、風險承受度、利潤空間及即時新聞，優先執行高優先級的交易。
* **專案管理**：人工智慧代理（AI Agent）會根據截止日期、相依關係、團隊可用性及策略重要性來排序看板上的任務。
* **網絡安全**：監控網絡流量的代理（Agent）會依據威脅嚴重程度、潛在影響及資產關鍵性來排序警示，以便即時回應最危險的威脅。
* **個人助理人工智慧**：透過優先排序管理日常生活，依據使用者定義的重要性、即將到期的期限及當前情境來整理行事曆、提醒及通知。

這些例子共同說明，優先排序能力是提升人工智慧代理（AI Agent）表現與決策能力的核心基礎，適用於各種情境。

## 動手做程式示例

以下示範如何使用 LangChain 開發一個專案經理人工智慧代理（Project Manager AI Agent）。此代理（Agent）協助建立、排序及分派任務予團隊成員，展示如何結合大型語言模型（Large Language Model）與自訂工具來實現自動化專案管理。

```python
import os
import asyncio
from typing import List, Optional, Dict, Type

from dotenv import load_dotenv
from pydantic import BaseModel, Field
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.tools import Tool
from langchain_openai import ChatOpenAI
from langchain.agents import AgentExecutor, create_react_agent
from langchain.memory import ConversationBufferMemory


# --- 0. Configuration and Setup ---
# Loads the OPENAI_API_KEY from the .env file.
load_dotenv()

# The ChatOpenAI client automatically picks up the API key from the environment.
llm = ChatOpenAI(temperature=0.5, model="gpt-4o-mini")


# --- 1. Task Management System ---
class Task(BaseModel):
    """Represents a single task in the system."""
    id: str
    description: str
    priority: Optional[str] = None  # P0, P1, P2
    assigned_to: Optional[str] = None  # Name of the worker


class SuperSimpleTaskManager:
    """An efficient and robust in-memory task manager."""

    def __init__(self):
        # Use a dictionary for O(1) lookups, updates, and deletions.
        self.tasks: Dict[str, Task] = {}
        self.next_task_id = 1

    def create_task(self, description: str) -> Task:
        """Creates and stores a new task."""
        task_id = f"TASK-{self.next_task_id:03d}"
        new_task = Task(id=task_id, description=description)
        self.tasks[task_id] = new_task
        self.next_task_id += 1
        print(f"DEBUG: Task created - {task_id}: {description}")
        return new_task

    def update_task(self, task_id: str, **kwargs) -> Optional[Task]:
        """Safely updates a task using Pydantic's model_copy."""
        task = self.tasks.get(task_id)
        if task:
            # Use model_copy for type-safe updates.
            update_data = {k: v for k, v in kwargs.items() if v is not None}
            updated_task = task.model_copy(update=update_data)
            self.tasks[task_id] = updated_task
            print(f"DEBUG: Task {task_id} updated with {update_data}")
            return updated_task

        print(f"DEBUG: Task {task_id} not found for update.")
        return None

    def list_all_tasks(self) -> str:
        """Lists all tasks currently in the system."""
        if not self.tasks:
            return "No tasks in the system."

        task_strings = []
        for task in self.tasks.values():
            task_strings.append(
                f"ID: {task.id}, Desc: '{task.description}', "
                f"Priority: {task.priority or 'N/A'}, "
                f"Assigned To: {task.assigned_to or 'N/A'}"
            )
        return "Current Tasks:\n" + "\n".join(task_strings)


task_manager = SuperSimpleTaskManager()


# --- 2. Tools for the Project Manager Agent ---
# Use Pydantic models for tool arguments for better validation and clarity.
class CreateTaskArgs(BaseModel):
    description: str = Field(description="A detailed description of the task.")


class PriorityArgs(BaseModel):
    task_id: str = Field(description="The ID of the task to update, e.g., 'TASK-001'.")
    priority: str = Field(description="The priority to set. Must be one of: 'P0', 'P1', 'P2'.")


class AssignWorkerArgs(BaseModel):
    task_id: str = Field(description="The ID of the task to update, e.g., 'TASK-001'.")
    worker_name: str = Field(description="The name of the worker to assign the task to.")


def create_new_task_tool(description: str) -> str:
    """Creates a new project task with the given description."""
    task = task_manager.create_task(description)
    return f"Created task {task.id}: '{task.description}'."


def assign_priority_to_task_tool(task_id: str, priority: str) -> str:
    """Assigns a priority (P0, P1, P2) to a given task ID."""
    if priority not in ["P0", "P1", "P2"]:
        return "Invalid priority. Must be P0, P1, or P2."
    task = task_manager.update_task(task_id, priority=priority)
    return f"Assigned priority {priority} to task {task.id}." if task else f"Task {task_id} not found."


def assign_task_to_worker_tool(task_id: str, worker_name: str) -> str:
    """Assigns a task to a specific worker."""
    task = task_manager.update_task(task_id, assigned_to=worker_name)
    return f"Assigned task {task.id} to {worker_name}." if task else f"Task {task_id} not found."


# All tools the PM agent can use
pm_tools = [
    Tool(
        name="create_new_task",
        func=create_new_task_tool,
        description="Use this first to create a new task and get its ID.",
        args_schema=CreateTaskArgs
    ),
    Tool(
        name="assign_priority_to_task",
        func=assign_priority_to_task_tool,
        description="Use this to assign a priority to a task after it has been created.",
        args_schema=PriorityArgs
    ),
    Tool(
        name="assign_task_to_worker",
        func=assign_task_to_worker_tool,
        description="Use this to assign a task to a specific worker after it has been created.",
        args_schema=AssignWorkerArgs
    ),
    Tool(
        name="list_all_tasks",
        func=task_manager.list_all_tasks,
        description="Use this to list all current tasks and their status."
    ),
]


# --- 3. Project Manager Agent Definition ---
pm_prompt_template = ChatPromptTemplate.from_messages([
    ("system", """You are a focused Project Manager LLM agent. Your goal is to manage project tasks efficiently.
      When you receive a new task request, follow these steps:
    1.  First, create the task with the given description using the `create_new_task` tool. You must do this first to get a `task_id`.
    2.  Next, analyze the user's request to see if a priority or an assignee is mentioned.
        - If a priority is mentioned (e.g., \"urgent\", \"ASAP\", \"critical\"), map it to P0. Use `assign_priority_to_task`.
        - If a worker is mentioned, use `assign_task_to_worker`.
    3.  If any information (priority, assignee) is missing, you must make a reasonable default assignment (e.g., assign P1 priority and assign to 'Worker A').
    4.  Once the task is fully processed, use `list_all_tasks` to show the final state.

    Available workers: 'Worker A', 'Worker B', 'Review Team'
    Priority levels: P0 (highest), P1 (medium), P2 (lowest)
    """),
    ("placeholder", "{chat_history}"),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}")
])

# Create the agent executor
pm_agent = create_react_agent(llm, pm_tools, pm_prompt_template)
pm_agent_executor = AgentExecutor(
    agent=pm_agent,
    tools=pm_tools,
    verbose=True,
    handle_parsing_errors=True,
    memory=ConversationBufferMemory(memory_key="chat_history", return_messages=True)
)


# --- 4. Simple Interaction Flow ---
async def run_simulation():
    print("--- Project Manager Simulation ---")

    # Scenario 1: Handle a new, urgent feature request
    print("\n[User Request] I need a new login system implemented ASAP. It should be assigned to Worker B.")
    await pm_agent_executor.ainvoke({"input": "Create a task to implement a new login system. It's urgent and should be assigned to Worker B."})

    print("\n" + "-" * 60 + "\n")

    # Scenario 2: Handle a less urgent content update with fewer details
    print("[User Request] We need to review the marketing website content.")
    await pm_agent_executor.ainvoke({"input": "Manage a new task: Review marketing website content."})

    print("\n--- Simulation Complete ---")


# Run the simulation
if __name__ == "__main__":
    asyncio.run(run_simulation())
```

此程式碼使用 Python 與 LangChain 實作一個簡易的任務管理系統，以模擬由大型語言模型（Large Language Model）驅動的專案經理代理（Project Manager Agent）。

系統運用 SuperSimpleTaskManager 類別在記憶體內有效管理任務，並透過字典結構快速存取資料。每個任務以 Pydantic 的 Task 模型表示，涵蓋唯一識別碼、描述文字、可選的優先級（P0、P1、P2）及可選的指派對象。記憶體使用量會因任務類型、工作人數及其他因素而異。此任務管理器提供建立任務、修改任務及列出所有任務的方法。

代理（Agent）透過一組預先定義的工具（Tool）與任務管理器互動。這些工具協助建立新任務、為任務設定優先級、將任務分派給人員，以及列出所有任務。每個工具都封裝成可與 SuperSimpleTaskManager 實例互動的介面，並運用 Pydantic 模型定義所需參數，以確保資料驗證。

我們設定一個代理執行器（AgentExecutor），將語言模型、工具組及對話記憶元件組合起來，以維持語境連貫性。同時定義特定的聊天提示模板（ChatPromptTemplate），引導代理（Agent）在其專案管理角色中的行為。提示會指示代理（Agent）先建立任務，再按照需求指定優先級與負責人，最後輸出完整的任務清單。對於欠缺資訊的情況，提示亦預設優先級為 P1 並指派給「Worker A」。

程式碼包含一個非同步的模擬函式（`run_simulation`），用以展示代理（Agent）的運作能力。模擬執行兩個不同情境：一項有指定人員的緊急任務，以及一項資訊較少、不那麼急迫的任務。由於在代理執行器（AgentExecutor）中啟用了 `verbose=True`，代理（Agent）的行動與推理流程會輸出至主控台。

# 重點速覽

**內容為何：** 在複雜環境中運作的人工智慧代理（AI Agent）需要面對大量潛在行動、互相衝突的目標，以及有限的資源。若沒有明確方法去決定下一步，這些代理（Agent）可能變得低效甚至失效，導致營運延誤，或無法完成主要目標。核心挑戰在於管理過多的選項，確保代理（Agent）行動具備目的性及邏輯性。

**為何重要：** 優先次序設計模式（Prioritization pattern）提供一套標準化的解決方案，讓代理（Agent）可以根據緊急性、重要性、相依性及資源成本等清晰準則來排序任務和目標。代理（Agent）會按這些準則評估每項潛在行動，從而找出最關鍵、最適時的行動方案。這種代理能力（Agentic capability）讓系統能夠因應環境變化而動態調整，並有效管理受限資源。透過集中處理最高優先級的項目，代理（Agent）的行為會更智能、更穩健，亦更貼近其策略目標。

**經驗法則：** 當代理系統（Agentic system）需要在資源受限的動態環境中，自主管理多項經常互相衝突的任務或目標時，就應使用優先次序設計模式（Prioritization pattern）。

**視覺摘要：**

**![Prioritization Design Pattern](../assets/Prioritization_Design_Pattern.png )

圖1：優先次序設計模式（Prioritization Design Pattern）

# 重要提示

* 優先排序讓人工智慧代理（AI Agent）在複雜、多元的環境中保持高效運作。
* 代理（Agent）會利用緊急性、重要性及相依性等既定準則來評估並排列任務。
* 動態再優先排序讓代理（Agent）可以隨著即時變化調整操作焦點。
* 優先排序涉及多個層次，包括整體策略目標與即時戰術決策。
* 有效的優先排序能提升人工智慧代理（AI Agent）的效率及營運穩健性。

# 結論

總括而言，優先次序設計模式（Prioritization pattern）是高效代理式人工智慧（Agentic AI）的基石，使系統能夠有目的、有智慧地應對動態環境的複雜性。此模式讓代理（Agent）可以自動評估眾多互相衝突的任務與目標，並合理決定應將有限資源投放在哪裡。

這種代理能力（Agentic capability）超越了單純的任務執行，使系統能主動作出策略決策。透過衡量緊急性、重要性與相依性等準則，代理（Agent）展現出類似人類的進階推理過程。

此類代理行為的一大特點是動態再優先排序，讓代理（Agent）能在環境變化時即時調整焦點。如同程式碼示例所示，代理（Agent）會解讀模糊的請求，自主挑選並運用合適的工具，並以合乎邏輯的順序執行行動以達成目標。

這種自我管理工作流程的能力，使真正的代理式系統（Agentic system）有別於簡單的自動化腳本。最終，掌握優先排序是打造能在複雜現實情境中可靠運作的穩健智能代理（Intelligent Agent）的關鍵。

# References

1. Examining the Security of Artificial Intelligence in Project Management: A Case Study of AI-driven Project Scheduling and Resource Allocation in Information Systems Projects ; [https://www.irejournals.com/paper-details/1706160](https://www.irejournals.com/paper-details/1706160)
2. AI-Driven Decision Support Systems in Agile Software Project Management: Enhancing Risk Mitigation and Resource Allocation; [https://www.mdpi.com/2079-8954/13/3/208](https://www.mdpi.com/2079-8954/13/3/208)
