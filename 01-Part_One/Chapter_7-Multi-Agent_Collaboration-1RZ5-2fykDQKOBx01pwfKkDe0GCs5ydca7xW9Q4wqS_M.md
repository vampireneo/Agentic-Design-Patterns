# 第七章：多代理協作（Multi-Agent Collaboration）

雖然單體代理架構在處理界定清晰的問題時或能奏效，但在面對複雜且跨領域的任務時，其能力往往受到限制。「多代理協作（Multi-Agent Collaboration）」模式通過將系統結構化為一個由多個具備專長的代理組成的協同團隊，以應對這些不足。此方法建立在任務分解的原則之上，先把高層目標拆分為離散的次級問題，再把各個次級問題指派給擁有相應工具、資料存取能力或推理能力的代理。

舉例而言，一項複雜的研究查詢可被拆分後交由研究代理負責資訊檢索、數據分析代理處理統計運算，以及綜合代理編寫最終報告。這種系統的效能不僅來自分工，更有賴於代理之間的通訊機制。為此，需要建立標準化的通訊協定（communication protocol）及共用本體（shared ontology），以便代理交換資料、委派子任務並協調行動，確保最終輸出的一致性。

這種分散式架構具有多項優勢，包括模組化程度更高、可擴展性更佳，以及穩健性更強；即使單一代理失效，整體系統亦不致全面停擺。透過協作，整個多代理系統的整體表現能超越任何單一代理的潛在能力。

## 多代理協作模式概覽

多代理協作模式旨在設計多個獨立或半獨立代理共同合作以達成共同目標的系統。每個代理通常擁有明確角色、與整體目標對齊的個別目標，並可能存取不同的工具或知識庫。此模式的力量在於代理之間的互動與協同效應。

協作形式多樣：

* **循序交接（Sequential Handoffs）：** 一個代理完成任務後把輸出交給下一個代理於管線中接續處理（類似規劃模式（Planning pattern），但明確涉及不同代理）。
* **平行處理（Parallel Processing）：** 多個代理同時處理問題的不同部分，之後再整合結果。
* **辯論與共識（Debate and Consensus）：** 多代理協作讓擁有不同觀點與資訊來源的代理展開討論，評估方案，最終達成共識或得出更充分的決策。
* **階層式結構（Hierarchical Structures）：** 管理者代理可根據工具存取或外掛能力動態委派任務給工作代理，並整合其結果。每個代理可掌管相關工具組，而非由單一代理處理所有工具。
* **專家團隊（Expert Teams）：** 不同領域的專家代理（如研究員、撰稿者、編輯）合作產出複雜輸出。
* **評論—審核（Critic-Reviewer）：** 代理先產生計劃、草稿或答案等初稿，再由另一組代理從政策、資安、合規、正確性、品質及組織目標對齊等角度批判性審視。原始創作者或最終代理會依據回饋修訂輸出。此模式對程式碼生成、研究寫作、邏輯檢查及倫理對齊特別有效，能提升穩健性與品質，並降低幻覺或錯誤風險。

多代理系統（參見圖一）基本上包含界定代理角色與職責、建立代理交換資訊的通訊渠道，以及擬定引導協作工作的任務流程或互動協定。

![多代理系統（Multi-Agent System）](../assets/Multi_Agent_System.png)

圖一：多代理系統示例。

Crew AI 與 Google ADK 等框架被設計來促成此範式，提供定義代理、任務及其互動流程的結構。此方法對需要多種專業知識、涵蓋多個離散階段，或能從平行處理與跨代理資訊互證中受益的挑戰尤為有效。

## 實務應用與案例

多代理協作是橫跨多個領域的強大模式：

* **複雜研究與分析：** 一組代理可合作進行研究項目。一個代理專責搜尋學術資料庫，另一個代理概述發現，第三個代理辨識趨勢，第四個代理把資訊綜合成報告，類似人類研究團隊的運作。
* **軟件開發（Software Development）：** 想像代理協作建立軟件：一個代理擔任需求分析師，另一個生成程式碼，第三個執行測試，第四個撰寫文件，互相傳遞輸出以建構與驗證元件。
* **創意內容生成：** 規劃行銷活動可由市場研究代理、文案撰稿代理、圖像設計代理（利用圖像生成工具）及社交媒體排程代理共同合作。
* **金融分析（Financial Analysis）：** 多代理系統可分析金融市場；代理可專長於抓取股票數據、分析新聞情緒、執行技術分析及提出投資建議。
* **客戶支援升級處理：** 前線支援代理處理初階查詢，必要時將複雜議題交由專家代理（如技術專家或帳務專家），展現基於問題複雜度的循序交接。
* **供應鏈優化（Supply Chain Optimization）：** 代理可代表供應鏈中的不同節點（供應商、製造商、分銷商），協作優化庫存、物流與排程，以回應需求變化或干擾。
* **網絡分析與修復（Network Analysis & Remediation）：** 自主營運非常受益於代理架構，尤其在故障定位。多個代理可協作分流及修復問題，建議最佳行動；亦能與傳統機器學習模型及工具整合，在發揮生成式人工智能優勢的同時善用現有系統。

能夠界定專門代理並精心編排其互動關係，使開發者得以建構具備更高模組化、可擴展性及應對複雜度能力的系統，而這些複雜度對單一整合代理而言可能不可逾越。

## 多代理協作：探索互動關係與通訊結構

理解代理互動與通訊的精細方式，是設計有效多代理系統的基礎。如圖二所示，存在一系列從最簡單單一代理場景到複雜、客製化協作框架的互動與通訊模型。各模型提供獨特優勢與挑戰，影響多代理系統的整體效率、穩健性與適應力。

### 1. 單一代理（Single Agent）

在最基本層次，「單一代理」在沒有與其他實體直接互動或溝通的情況下自主運作。此模型易於實作與管理，但其能力受限於個別代理的範疇與資源。適用於可分解為獨立子問題且各自可由單一自給自足代理解決的任務。

### 2. 網絡（Network）

「網絡」模型是邁向協作的重要一步，多個代理以去中心化方式彼此直接互動。通訊通常為點對點，得以共享資訊、資源甚至任務。此模型促進韌性，因為即使某個代理失效亦不一定癱瘓整體系統。然而，在大型且未結構化的網絡中管理通訊負荷並維持一致決策可能具挑戰。

### 3. 監督者（Supervisor）

在「監督者」模型中，專責代理「監督者」負責監督並協調一群下屬代理的活動。監督者充當通訊、任務分配與衝突解決的中心節點。此階層式結構提供清晰權責線並能簡化管理與控制；但它引入單點故障（single point of failure），若監督者面臨大量下屬或複雜任務亦可能成為瓶頸。

### 4. 工具化監督者（Supervisor as a Tool）

此模型是「監督者」概念的精細延伸，監督者角色較少直接指揮控制，而是提供資源、指引或分析支援。監督者可能提供工具、資料或計算服務，協助其他代理更有效完成任務，而不必事事下達指令。此方法旨在運用監督者能力，同時避免僵硬的自上而下控制。

### 5. 階層式（Hierarchical）

「階層式」模型擴充監督者概念，建立多層組織結構。包括多層監督者：高階監督者監管低階監督者，最底層則由一系列執行代理構成。此結構適用於可拆解為各層負責的次級問題的複雜課題，提供具結構性的擴展性與複雜度管理，允許在既定邊界內的分散決策。

![代理以多種方式溝通與互動（Agents Communicate and Interact in Various Ways）](../assets/Agents_Communicate_and_Interact_in_Various_Ways.png)

圖二：代理透過多種方式溝通與互動。

### 6. 客製化（Custom）

「客製化」模型代表多代理系統設計的最大彈性，可根據特定問題或應用的需求，打造獨特的互動與通訊結構。此模型可結合前述模型的元素形成混合方式，或因環境約束與機會而衍生全新設計。客製化模型通常源自優化特定效能指標、處理高度動態環境，或把領域專屬知識納入系統架構的需求。設計與實作客製化模型需要深入掌握多代理系統原理，並仔細考量通訊協定、協調機制與湧現行為。

總括而言，多代理系統的互動與通訊模型是關鍵設計抉擇。每種模型各有優劣，而最佳選擇取決於任務複雜度、代理數量、所需自主程度、韌性需求以及可接受的通訊負荷等因素。未來多代理系統的發展，勢必持續探索與優化這些模型，並開發全新協作智能範式。

## 實作範例程式（Crew AI）

以下 Python 程式展示如何使用 CrewAI 框架建立一個由人工智能驅動的團隊，生成有關人工智能趨勢的部落格文章。程式會先設定環境並從 .env 檔載入 API 金鑰。核心部分定義兩個代理：研究員負責發掘及摘要人工智能趨勢，作者則根據研究結果撰寫文章。

程式相應定義兩項任務：研究趨勢與撰寫文章，其中撰寫任務需依賴研究任務的輸出。這些代理與任務組成一個團隊（Crew），指定按序流程（sequential process），使任務依序執行。團隊使用最新的「gemini-2.0-flash」語言模型（language model）。主要函式透過 kickoff() 方法啟動該團隊，協調代理協作以產生所需輸出，最後列印最終結果。

```python
import os
from dotenv import load_dotenv
from crewai import Agent, Task, Crew, Process
from langchain_google_genai import ChatGoogleGenerativeAI


def setup_environment():
    """Loads environment variables and checks for the required API key."""
    load_dotenv()
    if not os.getenv("GOOGLE_API_KEY"):
        raise ValueError("GOOGLE_API_KEY not found. Please set it in your .env file.")


def main():
    """
    Initializes and runs the AI crew for content creation using the latest Gemini model.
    """
    setup_environment()

    # Define the language model to use.
    # Updated to a model from the Gemini 2.0 series for better performance and features.
    # For cutting-edge (preview) capabilities, you could use "gemini-2.5-flash".
    llm = ChatGoogleGenerativeAI(model="gemini-2.0-flash")

    # Define Agents with specific roles and goals
    researcher = Agent(
        role='Senior Research Analyst',
        goal='Find and summarize the latest trends in AI.',
        backstory="You are an experienced research analyst with a knack for identifying key trends and synthesizing information.",
        verbose=True,
        allow_delegation=False,
    )

    writer = Agent(
        role='Technical Content Writer',
        goal='Write a clear and engaging blog post based on research findings.',
        backstory="You are a skilled writer who can translate complex technical topics into accessible content.",
        verbose=True,
        allow_delegation=False,
    )

    # Define Tasks for the agents
    research_task = Task(
        description="Research the top 3 emerging trends in Artificial Intelligence in 2024-2025. Focus on practical applications and potential impact.",
        expected_output="A detailed summary of the top 3 AI trends, including key points and sources.",
        agent=researcher,
    )

    writing_task = Task(
        description="Write a 500-word blog post based on the research findings. The post should be engaging and easy for a general audience to understand.",
        expected_output="A complete 500-word blog post about the latest AI trends.",
        agent=writer,
        context=[research_task],
    )

    # Create the Crew
    blog_creation_crew = Crew(
        agents=[researcher, writer],
        tasks=[research_task, writing_task],
        process=Process.sequential,
        llm=llm,
        verbose=2,  # Set verbosity for detailed crew execution logs
    )

    # Execute the Crew
    print("## Running the blog creation crew with Gemini 2.0 Flash... ##")
    try:
        result = blog_creation_crew.kickoff()
        print("\n------------------\n")
        print("## Crew Final Output ##")
        print(result)
    except Exception as e:
        print(f"\nAn unexpected error occurred: {e}")


if __name__ == "__main__":
    main()
```

我們接下來將進一步探討 Google ADK 框架中的案例，重點聚焦階層式、平行式與循序式協調範式，以及將代理作為操作工具的實作。

## 實作範例程式（Google ADK）

以下程式示範如何在 Google ADK 中建立階層式代理結構，創建父子關係。程式定義兩種代理：LlmAgent 與自 BaseAgent 繼承的自訂 TaskExecutor。TaskExecutor 用於特定的非大型語言模型任務，示例中僅輸出「Task finished successfully」事件。名為 greeter 的 LlmAgent 以指定模型初始化，並設定為友善迎賓。自訂 TaskExecutor 實例化為 `task_doer`。父級 LlmAgent coordinator 同樣指定模型與指令，其指令指導其把問候任務委派給 greeter，而把任務執行交給 `task_doer`。greeter 與 `task_doer` 被加入 coordinator 的子代理列表，建立父子關係。程式最後確認關係正確並輸出成功訊息。

```python
from typing import AsyncGenerator

from google.adk.agents import LlmAgent, BaseAgent
from google.adk.agents.invocation_context import InvocationContext
from google.adk.events import Event


# Correctly implement a custom agent by extending BaseAgent
class TaskExecutor(BaseAgent):
    """A specialized agent with custom, non-LLM behavior."""
    name: str = "TaskExecutor"
    description: str = "Executes a predefined task."

    async def _run_async_impl(self, context: InvocationContext) -> AsyncGenerator[Event, None]:
        """Custom implementation logic for the task."""
        # This is where your custom logic would go.
        # For this example, we'll just yield a simple event.
        yield Event(author=self.name, content="Task finished successfully.")


# Define individual agents with proper initialization
# LlmAgent requires a model to be specified.
greeter = LlmAgent(
    name="Greeter",
    model="gemini-2.0-flash-exp",
    instruction="You are a friendly greeter.",
)

# Instantiate our concrete custom agent
task_doer = TaskExecutor()

# Create a parent agent and assign its sub-agents
# The parent agent's description and instructions should guide its delegation logic.
coordinator = LlmAgent(
    name="Coordinator",
    model="gemini-2.0-flash-exp",
    description="A coordinator that can greet users and execute tasks.",
    instruction="When asked to greet, delegate to the Greeter. When asked to perform a task, delegate to the TaskExecutor.",
    sub_agents=[
        greeter,
        task_doer,
    ],
)

# The ADK framework automatically establishes the parent-child relationships.
# These assertions will pass if checked after initialization.
assert greeter.parent_agent == coordinator
assert task_doer.parent_agent == coordinator

print("Agent hierarchy created successfully.")
```

以下程式說明如何在 Google ADK 框架中運用 LoopAgent 建立迭代工作流程。程式定義兩個代理：ConditionChecker 與 ProcessingStep。ConditionChecker 為自訂代理，負責檢查工作階段（session）狀態中的「status」值；若為「completed」則升級事件以停止迴圈，否則輸出事件以繼續迴圈。`ProcessingStep` 是使用「gemini-2.0-flash-exp」模型的 LlmAgent，其指令要求執行任務，若為最後一步則將工作階段的 `status` 設為「completed」。LoopAgent 名為 StatusPoller，設定 `max_iterations=10`，並包含 ProcessingStep 與 ConditionChecker 實例為子代理。LoopAgent 會依序執行子代理，最多 10 次迭代，若 ConditionChecker 判定狀態為「completed」則停止。

```python
import asyncio
from typing import AsyncGenerator

from google.adk.agents import LoopAgent, LlmAgent, BaseAgent
from google.adk.events import Event, EventActions
from google.adk.agents.invocation_context import InvocationContext


# Best Practice: Define custom agents as complete, self-describing classes.
class ConditionChecker(BaseAgent):
    """A custom agent that checks for a 'completed' status in the session state."""
    name: str = "ConditionChecker"
    description: str = "Checks if a process is complete and signals the loop to stop."

    async def _run_async_impl(
        self, context: InvocationContext
    ) -> AsyncGenerator[Event, None]:
        """Checks state and yields an event to either continue or stop the loop."""
        status = context.session.state.get("status", "pending")
        is_done = status == "completed"

        if is_done:
            # Escalate to terminate the loop when the condition is met.
            yield Event(author=self.name, actions=EventActions(escalate=True))
        else:
            # Yield a simple event to continue the loop.
            yield Event(author=self.name, content="Condition not met, continuing loop.")


# Correction: The LlmAgent must have a model and clear instructions.
process_step = LlmAgent(
    name="ProcessingStep",
    model="gemini-2.0-flash-exp",
    instruction=(
        "You are a step in a longer process. Perform your task. "
        "If you are the final step, update session state by setting 'status' to 'completed'."
    ),
)


# The LoopAgent orchestrates the workflow.
poller = LoopAgent(
    name="StatusPoller",
    max_iterations=10,
    sub_agents=[
        process_step,
        ConditionChecker(),  # Instantiating the well-defined custom agent.
    ],
)

# This poller will now execute 'process_step'
# and then 'ConditionChecker' repeatedly until the status is 'completed'
# or 10 iterations have passed.
```

以下程式闡釋 Google ADK 中的 SequentialAgent 模式，設計線性工作流程。此程式使用 google.adk.agents 函式庫定義兩個代理 step1 與 step2。step1 名為 `Step1_Fetch`，其輸出會儲存在工作階段狀態的 `data` 鍵值下；step2 名為 `Step2_Process`，指示其分析儲存在 `session.state["data"]` 的資訊並提供摘要。SequentialAgent 名為「MyPipeline」，負責編排子代理的執行。當管線以初始輸入啟動時，step1 先執行並把回應存入 `session.state["data"]`，隨後 step2 執行並依指示使用該資訊。此結構允許建立多步驟人工智能或資料處理管線，其中一個代理的輸出成為下一個代理的輸入。

```python
from google.adk.agents import SequentialAgent, Agent


# This agent's output will be saved to session.state["data"]
step1 = Agent(
    name="Step1_Fetch",
    output_key="data",
)

# This agent will use the data from the previous step.
# We instruct it on how to find and use this data.
step2 = Agent(
    name="Step2_Process",
    instruction="Analyze the information found in state['data'] and provide a summary.",
)

pipeline = SequentialAgent(
    name="MyPipeline",
    sub_agents=[step1, step2],
)

# When the pipeline is run with an initial input, Step1 will execute,
# its response will be stored in session.state["data"], and then
# Step2 will execute, using the information from the state as instructed.
```

以下程式示範 Google ADK 中的 ParallelAgent 模式，可並行執行多個代理任務。`data_gatherer` 旨在同時執行兩個子代理：`weather_fetcher` 與 `news_fetcher`。`weather_fetcher` 指示為取得指定地點的天氣並將結果存入 `session.state["weather_data"]`；`news_fetcher` 則取得指定主題的頭條新聞並存入 `session.state["news_data"]`。每個子代理皆使用「gemini-2.0-flash-exp」模型。ParallelAgent 協調子代理的平行工作，並在執行完成後於最終狀態（final state）中讀取天氣與新聞資料。

```python
from google.adk.agents import Agent, ParallelAgent


# It's better to define the fetching logic as tools for the agents.
# For simplicity in this example, we'll embed the logic in the agent's instruction.
# In a real-world scenario, you would use tools.

# Define the individual agents that will run in parallel
weather_fetcher = Agent(
    name="weather_fetcher",
    model="gemini-2.0-flash-exp",
    instruction="Fetch the weather for the given location and return only the weather report.",
    output_key="weather_data",  # The result will be stored in session.state["weather_data"]
)

news_fetcher = Agent(
    name="news_fetcher",
    model="gemini-2.0-flash-exp",
    instruction="Fetch the top news story for the given topic and return only that story.",
    output_key="news_data",  # The result will be stored in session.state["news_data"]
)

# Create the ParallelAgent to orchestrate the sub-agents
data_gatherer = ParallelAgent(
    name="data_gatherer",
    sub_agents=[
        weather_fetcher,
        news_fetcher,
    ],
)
```

上述程式片段示範「代理即工具（Agent as a Tool）」範式，使代理能以類似函式呼叫的方式運用另一個代理的能力。程式利用 Google 的 LlmAgent 與 AgentTool 類別建立圖像生成系統，包括父級 `artist_agent` 與子級 `image_generator_agent`。`generate_image` 函式作為簡單工具，模擬圖像生成並回傳模擬資料。`image_generator_agent` 會依收到的文字提示運用該工具；`artist_agent` 則先構想創意圖像提示，再透過 AgentTool 包裝呼叫 `image_generator_agent`。當 `artist_agent` 透過 `image_tool` 呼叫時，AgentTool 會以創作的提示喚起 `image_generator_agent`，後者使用 `generate_image` 函式生成圖像並將結果回傳。此架構展示高階代理如何編排低階專業代理執行任務。

```python
from google.adk.agents import LlmAgent
from google.adk.tools import agent_tool
from google.genai import types


# 1. A simple function tool for the core capability.
# This follows the best practice of separating actions from reasoning.
def generate_image(prompt: str) -> dict:
    """
    Generates an image based on a textual prompt.

    Args:
        prompt: A detailed description of the image to generate.

    Returns:
        A dictionary with the status and the generated image bytes.
    """
    print(f"TOOL: Generating image for prompt: '{prompt}'")
    # In a real implementation, this would call an image generation API.
    # For this example, we return mock image data.
    mock_image_bytes = b"mock_image_data_for_a_cat_wearing_a_hat"
    return {
        "status": "success",
        # The tool returns the raw bytes, the agent will handle the Part creation.
        "image_bytes": mock_image_bytes,
        "mime_type": "image/png",
    }


# 2. Refactor the ImageGeneratorAgent into an LlmAgent.
# It now correctly uses the input passed to it.
image_generator_agent = LlmAgent(
    name="ImageGen",
    model="gemini-2.0-flash",
    description="Generates an image based on a detailed text prompt.",
    instruction=(
        "You are an image generation specialist. Your task is to take the user's request "
        "and use the `generate_image` tool to create the image. "
        "The user's entire request should be used as the 'prompt' argument for the tool. "
        "After the tool returns the image bytes, you MUST output the image."
    ),
    tools=[generate_image],
)


# 3. Wrap the corrected agent in an AgentTool.
# The description here is what the parent agent sees.
image_tool = agent_tool.AgentTool(
    agent=image_generator_agent,
    description="Use this tool to generate an image. The input should be a descriptive prompt of the desired image.",
)


# 4. The parent agent remains unchanged. Its logic was correct.
artist_agent = LlmAgent(
    name="Artist",
    model="gemini-2.0-flash",
    instruction=(
        "You are a creative artist. First, invent a creative and descriptive prompt for an image. "
        "Then, use the `ImageGen` tool to generate the image using your prompt."
    ),
    tools=[image_tool],
)
```

## 重點速覽（At a Glance）

**內容：** 複雜問題往往超出單一、單體大型語言模型代理的能力。單一代理可能缺乏多樣專業技能或無法存取解決多面向任務所需的特定工具，造成瓶頸，降低系統效能與擴展性，使處理跨領域目標效率低下，甚至輸出不完整或欠佳。

**原因：** 多代理協作模式提供標準化解決方案，透過建立多個協作代理的系統來拆解複雜問題。把大型問題拆為較小且可管理的子問題，再指派給擁有相應工具與能力的專業代理。這些代理透過明確的通訊協定與互動模型（如循序交接、平行工作流程或階層式委派）協同合作，形成人工代理網絡（agentic, distributed approach），達成單一代理無法實現的成果。

**經驗法則：** 當任務過於複雜、可拆分為需要專業技能或工具的離散子任務時，宜採用此模式。特別適合受益於多元專業、平行處理或具多階段結構化工作流程的問題，如複雜研究與分析、軟件開發或創意內容生成。

**視覺摘要：**

![多代理設計模式（Multi-Agent Design Pattern）](../assets/Multi_Agent_Design_Pattern.png)

圖三：多代理設計模式。

## 重要提示（Key Takeaways）

* 多代理協作涉及多個代理合作以達成共同目標。
* 此模式運用專業化角色、分散任務與代理間通訊。
* 協作形式可為循序交接、平行處理、辯論或階層式結構。
* 此模式適合需要多元專業或多個明確階段的複雜問題。

## 結語（Conclusion）

本章探討多代理協作模式，闡明在系統中協調多個專業代理的優勢。我們檢視多種協作模型，強調此模式在應對橫跨多領域的複雜問題時的關鍵作用。理解代理協作自然而然引導我們進一步探究其與外部環境的互動。

## References

1. Multi-Agent Collaboration Mechanisms: A Survey of LLMs, [https://arxiv.org/abs/2501.06322](https://arxiv.org/abs/2501.06322)
2. Multi-Agent System — The Power of Collaboration, [https://aravindakumar.medium.com/introducing-multi-agent-frameworks-the-power-of-collaboration-e9db31bba1b6](https://aravindakumar.medium.com/introducing-multi-agent-frameworks-the-power-of-collaboration-e9db31bba1b6)
