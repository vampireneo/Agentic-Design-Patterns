# 附錄 C - 代理式框架快速概覽（Agentic Frameworks）

## LangChain

LangChain 框架（LangChain）是一個用於開發由大型語言模型（Large Language Models, LLMs）驅動的應用程式的框架。其核心優勢在於 LangChain 表達語言（LangChain Expression Language, LCEL），可讓你把元件串接成一條鏈。這樣可建立清晰、線性的流程，讓每一步的輸出成為下一步的輸入。它特別適合建構有向無環圖（Directed Acyclic Graph, DAG）的工作流程，即過程只會單向流動而不會出現迴圈。

適用於：

* 簡單的檢索增強生成（Retrieval-Augmented Generation, RAG）：擷取文件、建立提示詞，並從大型語言模型（LLM）取得答案。
* 摘要：接收使用者文字，送入摘要提示，然後輸出結果。
* 資料抽取：從一段文字抽取結構化資料（例如 JSON）。

Python

```python
# 概念層面的 LCEL 鏈示意（非可執行程式碼，只作流程說明）
chain = prompt | model | output_parse
```

## LangGraph

LangGraph 程式庫（LangGraph）建基於 LangChain 之上，用來處理更進階的代理式系統。它讓你把工作流程定義為一個圖，其節點代表函式或 LCEL 鏈，邊則代表條件邏輯。其主要優勢是可以建立迴圈，讓應用程式在完成任務前能夠循環、重試或依需要調用工具。它會顯式管理應用程式狀態，該狀態會在節點之間傳遞並於過程中更新。

適用於：

* 多代理系統（Multi-agent Systems）：主管代理會把任務分派給專責代理，並可能循環直至達成目標。

* 計劃與執行代理（Plan-and-Execute Agents）：代理會先建立計劃、執行一步，再根據結果更新計劃並循環。

* 人類介入（Human-in-the-Loop）：圖可以等待人類輸入，再決定下一個節點。

| 功能 | LangChain | LangGraph |
| :---- | :---- | :---- |
| 核心抽象 | 使用 LCEL 的鏈 | 節點構成的圖 |
| 工作流程類型 | 線性（有向無環圖） | 可迴圈（含迴圈的圖） |
| 狀態管理 | 一般在每次執行時為無狀態 | 顯式且持久的狀態物件 |
| 主要用途 | 簡單、可預期的序列 | 複雜、動態且具狀態的代理 |

### 應該選擇哪一個？

* 當你的應用程式有清晰、可預期、線性的步驟流程時，選擇 LangChain。若你能從 A 走到 B 再到 C，而不需要迴圈，LangChain 配合 LCEL 就是理想工具。
* 當你需要應用程式進行推理、規劃或在迴圈中運作時，選擇 LangGraph。若代理需要使用工具、反思結果，並可能以不同方法再嘗試一次，你就需要 LangGraph 的迴圈與狀態特性。

```python
# 圖狀態
class State(TypedDict):
    topic: str
    joke: str
    story: str
    poem: str
    combined_output: str


# 節點
def call_llm_1(state: State):
    """第一次呼叫大型語言模型（LLM）以產生笑話"""
    msg = llm.invoke(f"Write a joke about {state['topic']}")
    return {"joke": msg.content}


def call_llm_2(state: State):
    """第二次呼叫大型語言模型（LLM）以產生故事"""
    msg = llm.invoke(f"Write a story about {state['topic']}")
    return {"story": msg.content}


def call_llm_3(state: State):
    """第三次呼叫大型語言模型（LLM）以產生詩歌"""
    msg = llm.invoke(f"Write a poem about {state['topic']}")
    return {"poem": msg.content}


def aggregator(state: State):
    """把笑話與故事結合成單一輸出"""
    combined = f"Here's a story, joke, and poem about {state['topic']}!\n\n"
    combined += f"STORY:\n{state['story']}\n\n"
    combined += f"JOKE:\n{state['joke']}\n\n"
    combined += f"POEM:\n{state['poem']}"
    return {"combined_output": combined}


# 建立工作流程
parallel_builder = StateGraph(State)

# 加入節點
parallel_builder.add_node("call_llm_1", call_llm_1)
parallel_builder.add_node("call_llm_2", call_llm_2)
parallel_builder.add_node("call_llm_3", call_llm_3)
parallel_builder.add_node("aggregator", aggregator)

# 加入邊以連接節點
parallel_builder.add_edge(START, "call_llm_1")
parallel_builder.add_edge(START, "call_llm_2")
parallel_builder.add_edge(START, "call_llm_3")
parallel_builder.add_edge("call_llm_1", "aggregator")
parallel_builder.add_edge("call_llm_2", "aggregator")
parallel_builder.add_edge("call_llm_3", "aggregator")
parallel_builder.add_edge("aggregator", END)

parallel_workflow = parallel_builder.compile()

# 顯示工作流程
display(Image(parallel_workflow.get_graph().draw_mermaid_png()))

# 執行
state = parallel_workflow.invoke({"topic": "cats"})
print(state["combined_output"])
```

這段程式碼定義並執行一個以平行方式運作的 LangGraph 工作流程。其主要目的，是同時產生一個關於指定主題的笑話、故事與詩歌，然後把它們合併成單一且格式化的文字輸出。

## Google 的 ADK

Google 的代理開發工具套件（Agent Development Kit, ADK）提供高層次且結構化的框架，用來建構與部署由多個互動式人工智能代理組成的應用程式。它與 LangChain 和 LangGraph 的對比，在於它提供更具主觀意見且以生產環境為導向的系統，用來協調代理之間的協作，而非提供代理內部邏輯的基礎組件。

LangChain 運作在最基礎的層次，提供元件與標準化介面，以建立一連串的操作，例如呼叫模型及解析輸出。LangGraph 在此基礎上引入更靈活且強大的控制流程，把代理的工作流程視為具狀態的圖。在使用 LangGraph 時，開發者會明確定義節點（函式或工具），以及決定執行路徑的邊。這種圖狀結構允許複雜且可迴圈的推理，系統可以迴圈、重試任務，並根據在節點之間傳遞的顯式管理狀態物件作出決策。它讓開發者能夠精細掌控單一代理的思路，或從零構建多代理系統。

Google 的 ADK 抽象化了許多低層次的圖形建構。它不要求開發者定義每一個節點與邊，而是提供多代理互動的預先建構架構模式。例如，ADK 內建的代理類型如 SequentialAgent 或 ParallelAgent，會自動管理不同代理之間的控制流程。它以「代理團隊」的概念為核心，通常由主要代理把任務分派給專門的子代理。狀態與工作階段的管理由框架較為隱性地處理，提供比 LangGraph 顯式狀態傳遞更完整但細節較少的方式。因此，LangGraph 讓你擁有設計單一機械人或團隊內部精細線路的工具，而 Google 的 ADK 則像是一條已建好的工廠生產線，用來建立並管理一批已經懂得協作的機械人。

```python
from google.adk.agents import LlmAgent
from google.adk.tools import google_Search

dice_agent = LlmAgent(
    model="gemini-2.0-flash-exp",
    name="question_answer_agent",
    description="A helpful assistant agent that can answer questions.",
    instruction="""Respond to the query using google search""",
    tools=[google_search],
)
```

這段程式碼建立了一個具搜尋增強能力的代理。當代理收到問題時，它不會只依賴既有知識，而是會按照指示使用 Google 搜尋工具，尋找相關且即時的網上資訊，再利用該資訊組成答案。

## Crew.AI

CrewAI 協作框架（CrewAI）專注於透過協作角色與結構化流程來建構多代理系統。它的抽象層次高於基礎工具集，提供一個模擬人類團隊的概念模型。開發者無需以圖形定義細緻的邏輯流程，只需定義參與者與其職責，CrewAI 便會管理他們的互動。

此框架的核心組件包括代理（Agents）、任務（Tasks）與團隊（Crew）。代理不僅由功能定義，還包含角色、目標與背景設定等人格元素，指引其行為與溝通風格。任務是具體的工作單位，擁有清晰描述與預期輸出，並指派給特定代理。團隊則是整合代理與任務清單的整體單位，並執行預先定義的流程。該流程通常是順序式（sequential），即上一個任務的輸出成為下一個任務的輸入；或是分層式（hierarchical），由具管理者角色的代理分派任務並協調其他代理的工作流程。

與其他框架相比，CrewAI 的定位十分鮮明。它遠離 LangGraph 中那種低層次、顯式狀態管理與控制流程的設計，開發者毋須連接每個節點與條件邊。開發者並非在建構狀態機，而是在擬定團隊章程。Google 的 ADK 雖然提供橫跨整個代理生命週期、面向生產環境的完整平台，CrewAI 則特別聚焦於代理協作邏輯，以及模擬專業團隊的運作。

```python
@crew
def crew(self) -> Crew:
   """建立研究團隊"""
   return Crew(
     agents=self.agents,
     tasks=self.tasks,
     process=Process.sequential,
     verbose=True,
   )
```

這段程式碼為一隊人工智能代理設定順序式工作流程，他們會按特定次序處理一系列任務，並啟用詳細紀錄以監察進度。

## 其他代理開發框架（Agent Development Framework）

**Microsoft AutoGen**：AutoGen 框架（AutoGen）專注於透過對話協調多個代理共同解決任務。其架構讓具有不同能力的代理互相交流，從而進行複雜的問題拆解與協同解決。AutoGen 的主要優勢是彈性高、以對話為核心，可支援動態且複雜的多代理互動。不過，這種對話式模式可能導致執行路徑較難預測，並可能需要精細的提示工程（Prompt Engineering）以確保任務有效收斂。

**LlamaIndex**：LlamaIndex 框架（LlamaIndex）本質上是一個數據框架，用來將大型語言模型（LLMs）與外部及私有數據來源連接。它擅長建立複雜的數據擷取與檢索流程，這對打造具知識的代理以執行檢索增強生成（RAG）至關重要。雖然它在數據索引與查詢方面的能力極為強大，可用來打造具情境感知的代理，但其原生的複雜代理式控制流程與多代理協作工具，較起以代理為核心的框架仍未臻成熟。當主要技術挑戰是數據檢索與整合時，LlamaIndex 是最佳選擇。
