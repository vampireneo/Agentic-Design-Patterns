# 第三章：並行化（Parallelization）

## 並行化模式概覽（Parallelization Pattern Overview）

在前幾章中，我們已經探討了用於順序工作流程的提示鏈結（Prompt Chaining）以及用於動態決策與路徑轉換的導向（Routing）。雖然這些模式相當重要，但許多複雜的代理任務會包含多個子工作，這些子工作可以*同時地（simultaneously）*執行，而非逐一完成。這正是**並行化（Parallelization）**模式至關重要之處。

並行化（Parallelization）涉及同時執行多個元件，例如大型語言模型呼叫（LLM calls）、工具使用（tool usages），甚至整個子代理（sub-agents）（見圖一）。與其等待前一步驟完成才啟動下一個步驟，並行執行能讓彼此獨立的任務同時運行，顯著縮短可拆解為獨立部分之工作的整體執行時間。

想像一個代理被設計用來研究某個主題並總結其發現。順序式方法可能會：

1. 搜尋來源 A。
2. 摘要來源 A。
3. 搜尋來源 B。
4. 摘要來源 B。
5. 由摘要 A 與摘要 B 合成最終答案。

而並行方式則可以改為：

1. 同時搜尋來源 A *與* 搜尋來源 B。
2. 一旦兩個搜尋都完成，就同時摘要來源 A *與* 摘要來源 B。
3. 由摘要 A 與摘要 B 合成最終答案（此步驟通常是順序的，需要等待並行步驟完成）。

核心理念是找出工作流程中彼此不相依賴的部分，並將它們並行執行。這在處理具有延遲的外部服務（例如應用程式介面（APIs）或資料庫（databases））時特別有效，因為可以同時發出多個請求。

實作並行化（Parallelization）通常需要支援非同步執行或多執行緒／多製程（multi-threading/multi-processing）的框架。現代的代理框架是以非同步操作為設計核心，使您能輕鬆定義可並行執行的步驟。

![並行化與子代理（Parallelization with Sub-Agents）](../assets/Parallelization_with_Sub_Agents.png)

圖一。具有子代理的並行化範例

例如 LangChain 框架（LangChain）、LangGraph 框架（LangGraph）與 Google 代理開發套件（Google ADK）等框架都提供並行執行機制。在 LangChain 表達語言（LangChain Expression Language，LCEL）中，您可以透過使用像 |（順序）等運算子結合可執行物件（runnable objects），並將鏈結（chains）或圖形（graphs）結構化為可以同時運行的分支，以達致並行執行。LangGraph 框架（LangGraph）透過圖形結構，允許您在單一狀態轉換中定義多個節點（nodes），實際上在工作流程中實現並行分支。Google 代理開發套件（Google ADK）提供穩健且原生的機制，以便促進並管理代理的並行執行，大幅提升複雜多代理系統的效率與可擴充性。這項 ADK 框架（ADK）的內建能力讓開發人員可以設計與實作多個代理同時（而非依序）運作的解決方案。

並行化（Parallelization）模式對於提升代理系統的效率與回應速度至關重要，尤其在需要多次獨立查詢、計算或與外部服務互動的工作上。它是優化複雜代理工作流程效能的關鍵技術之一。

## 實務應用與使用情境（Practical Applications & Use Cases）

並行化（Parallelization）是跨越多種應用以最佳化代理效能的強大模式：

### 1. 資訊收集與研究（Information Gathering and Research）

同時從多個來源收集資訊是經典使用情境。

* **使用情境：** 研究某間公司的代理。
  * **並行任務：** 同時搜尋新聞文章、擷取股票數據、檢視社交媒體提及，並查詢公司資料庫。
  * **效益：** 相較於逐一查詢，能更快速獲得全面觀點。

### 2. 數據處理與分析（Data Processing and Analysis）

同時套用不同的分析技術或並行處理不同的資料區段。

* **使用情境：** 分析顧客回饋的代理。
  * **並行任務：** 針對一批回饋項目，同步執行情感分析（sentiment analysis）、關鍵詞擷取（keyword extraction）、回饋分類（feedback categorization），以及辨識緊急議題（urgent issues）。
  * **效益：** 能快速提供多面向分析。

### 3. 多應用程式介面或工具互動（Multi-API or Tool Interaction）

呼叫多個彼此獨立的應用程式介面（APIs）或工具，以收集不同類型資訊或執行不同操作。

* **使用情境：** 旅遊規劃代理。
  * **並行任務：** 同時查詢機票價格、搜尋飯店空房、檢索當地活動，以及尋找餐廳推薦。
  * **效益：** 更快速地提供完整旅遊計畫。

### 4. 多元組件的內容生成（Content Generation with Multiple Components）

為複雜內容的不同部分進行並行生成。

* **使用情境：** 建構行銷電郵的代理。
  * **並行任務：** 同步產生主旨、撰寫電郵正文、尋找相關圖片，以及建立行動呼籲按鈕文字。
  * **效益：** 更有效率地組合最終電郵。

### 5. 驗證與核實（Validation and Verification）

並行執行多個獨立的檢查或驗證。

* **使用情境：** 核實使用者輸入的代理。
  * **並行任務：** 同時檢查電郵格式、驗證電話號碼、以資料庫核實地址，以及進行辱罵詞檢測。
  * **效益：** 更快速地提供輸入有效性的回饋。

### 6. 多模態處理（Multi-Modal Processing）

同時處理同一輸入的不同模態（例如文字、圖像、音訊）。

* **使用情境：** 分析含文字與圖像的社交媒體貼文的代理。
  * **並行任務：** 同步分析文字以取得情感與關鍵詞，並分析圖像以獲得物件與場景描述。
  * **效益：** 更快速整合不同模態的洞見。

### 7. A/B 測試或多選項生成（A/B Testing or Multiple Options Generation）

並行產生多個回應或輸出變體，以便挑選最佳選項。

* **使用情境：** 生成多種創意文字選項的代理。
  * **並行任務：** 透過稍有不同的提示或模型，同步產生三個文章標題。
  * **效益：** 允許迅速比較並選擇最佳方案。

並行化（Parallelization）是代理設計中的基本最佳化技術，透過對獨立任務的同時執行，讓開發人員能打造效能更佳且回應更靈敏的應用程式。

## 實作範例：LangChain（Hands-On Code Example (LangChain)）

在 LangChain 框架（LangChain）內，並行執行由 LangChain 表達語言（LangChain Expression Language，LCEL）所支援。主要方法是將多個可執行元件結構化於字典或清單（dictionary or list）中。當該集合作為輸入傳遞給後續鏈結元件時，LCEL 執行階段（LCEL runtime）會並行執行其中的可執行單元。

在 LangGraph 框架（LangGraph）的脈絡中，這項原則套用於圖形拓撲（graph topology）。透過設計，使得圖上缺乏直接順序相依的多個節點可以從同一個共通節點啟動，即可定義並行工作流程。這些並行路徑會獨立執行，然後在圖上後續的匯流點（convergence point）聚合結果。

以下實作展示一個使用 LangChain 框架（LangChain）建構的並行處理工作流程。此流程旨在針對單一使用者查詢，同時執行兩個獨立操作。這些並行程序被建立為獨立的鏈結或函式，之後其輸出會整合為統一結果。

此實作的先決條件包括安裝必要的 Python 套件，如 langchain（langchain）、langchain-community（langchain-community），以及如 langchain-openai（langchain-openai）等模型提供者函式庫。此外，必須在本機環境設定所選語言模型的有效應用程式介面金鑰（API key），以進行驗證。

```python
import os
import asyncio
from typing import Optional

from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import Runnable, RunnableParallel, RunnablePassthrough


# --- Configuration ---
# Ensure your API key environment variable is set (e.g., OPENAI_API_KEY)
try:
    llm: Optional[ChatOpenAI] = ChatOpenAI(model="gpt-4o-mini", temperature=0.7)
except Exception as e:
    print(f"Error initializing language model: {e}")
    llm = None


# --- Define Independent Chains ---
# These three chains represent distinct tasks that can be executed in parallel.
summarize_chain: Runnable = (
    ChatPromptTemplate.from_messages([
        ("system", "Summarize the following topic concisely:"),
        ("user", "{topic}"),
    ])
    | llm
    | StrOutputParser()
)

questions_chain: Runnable = (
    ChatPromptTemplate.from_messages([
        ("system", "Generate three interesting questions about the following topic:"),
        ("user", "{topic}"),
    ])
    | llm
    | StrOutputParser()
)

terms_chain: Runnable = (
    ChatPromptTemplate.from_messages([
        ("system", "Identify 5-10 key terms from the following topic, separated by commas:"),
        ("user", "{topic}"),
    ])
    | llm
    | StrOutputParser()
)


# --- Build the Parallel + Synthesis Chain ---
# 1. Define the block of tasks to run in parallel. The results of these,
#    along with the original topic, will be fed into the next step.
map_chain = RunnableParallel(
    {
        "summary": summarize_chain,
        "questions": questions_chain,
        "key_terms": terms_chain,
        "topic": RunnablePassthrough(),  # Pass the original topic through
    }
)

# 2. Define the final synthesis prompt which will combine the parallel results.
synthesis_prompt = ChatPromptTemplate.from_messages([
    (
        "system",
        """Based on the following information:
        Summary: {summary}
        Related Questions: {questions}
        Key Terms: {key_terms}
        Synthesize a comprehensive answer."""
    ),
    ("user", "Original topic: {topic}"),
])

# 3. Construct the full chain by piping the parallel results directly
#    into the synthesis prompt, followed by the LLM and output parser.
full_parallel_chain = map_chain | synthesis_prompt | llm | StrOutputParser()


# --- Run the Chain ---
async def run_parallel_example(topic: str) -> None:
    """
    Asynchronously invokes the parallel processing chain with a specific topic
    and prints the synthesized result.

    Args:
        topic: The input topic to be processed by the LangChain chains.
    """
    if not llm:
        print("LLM not initialized. Cannot run example.")
        return

    print(f"\n--- Running Parallel LangChain Example for Topic: '{topic}' ---")
    try:
        # The input to `ainvoke` is the single 'topic' string,
        # then passed to each runnable in the `map_chain`.
        response = await full_parallel_chain.ainvoke(topic)
        print("\n--- Final Response ---")
        print(response)
    except Exception as e:
        print(f"\nAn error occurred during chain execution: {e}")


if __name__ == "__main__":
    test_topic = "The history of space exploration"
    # In Python 3.7+, asyncio.run is the standard way to run an async function.
    asyncio.run(run_parallel_example(test_topic))
```

上述 Python 程式碼實作了一個運用並行執行的 LangChain 應用程式（LangChain），用以高效率處理指定主題。值得注意的是，asyncio（asyncio）提供的是併發（concurrency），而非平行處理（parallelism）。它在單一執行緒上透過事件迴圈（event loop）在任務閒置（例如等待網路請求）時智慧切換任務，營造多個任務同時進行的效果，但程式碼仍由單一執行緒執行，受限於 Python 全域直譯器鎖（Global Interpreter Lock，GIL）。

程式一開始匯入來自 `langchain_openai` 與 `langchain_core` 的重要模組，包括語言模型（language models）、提示（prompts）、輸出剖析（output parsing）與可執行結構（runnable structures）。程式嘗試初始化 ChatOpenAI（ChatOpenAI）實例，特別是使用 "gpt-4o-mini" 模型，並設定溫度以控制創意度。透過例外處理（try-except）確保語言模型初始化的穩健性。接著定義三個獨立的 LangChain 鏈結（LangChain chains），各自對輸入主題執行特定任務。第一個鏈結負責精簡摘要該主題，使用系統訊息與包含主題佔位符的使用者訊息。第二個鏈結則產生與主題相關的三個有趣問題。第三個鏈結設定為辨識五至十個關鍵詞，並要求以逗號分隔。每個獨立鏈結都包含為其任務量身打造的 ChatPromptTemplate（ChatPromptTemplate），接著是初始化的語言模型，以及將輸出格式化為字串的 StrOutputParser（StrOutputParser）。

接下來建立 RunnableParallel（RunnableParallel）區塊，將這三個鏈結打包，以便同時執行。此並行可執行單元也包含 RunnablePassthrough（RunnablePassthrough），以確保原始輸入主題可供後續步驟使用。之後定義獨立的合成提示（synthesis prompt），取用摘要、問題、關鍵詞與原始主題作為輸入，以產生全面答案。透過串接 `map_chain`（map_chain，並行區塊）、合成提示、語言模型與輸出剖析器，形成完整的 `full_parallel_chain`（full_parallel_chain）。

提供的非同步函式 `run_parallel_example`（run_parallel_example）示範如何呼叫 `full_parallel_chain`（full_parallel_chain）。此函式接收主題作為輸入，並使用 `ainvoke`（ainvoke）執行非同步鏈結。最後，標準的 Python `if __name__ == "__main__":` 區塊展示如何以範例主題「太空探索史」（"The history of space exploration"）搭配 asyncio.run（asyncio.run）執行此非同步流程。

總而言之，這段程式碼建立了一個工作流程，對單一主題同時進行多個大型語言模型呼叫（LLM calls），包括摘要、問題與關鍵詞產出，然後由最終大型語言模型呼叫彙整結果。這展示了在代理工作流程中運用 LangChain 框架（LangChain）實現並行化的核心概念。

## 實作範例：Google ADK（Hands-On Code Example (Google ADK)）

接下來，我們將焦點轉向在 Google 代理開發套件（Google ADK）框架中的具體範例。此處將檢視如何運用 ADK 原語（primitives），例如 ParallelAgent（ParallelAgent）與 SequentialAgent（SequentialAgent），構建能善用併發執行以提升效率的代理流程。

```python
from google.adk.agents import LlmAgent, ParallelAgent, SequentialAgent
from google.adk.tools import google_search

GEMINI_MODEL = "gemini-2.0-flash"


# --- 1. Define Researcher Sub-Agents (to run in parallel) ---

# Researcher 1: Renewable Energy
researcher_agent_1 = LlmAgent(
    name="RenewableEnergyResearcher",
    model=GEMINI_MODEL,
    instruction="""You are an AI Research Assistant specializing in energy. Research the latest advancements in 'renewable energy sources'. Use the Google Search tool provided. Summarize your key findings concisely (1-2 sentences). Output *only* the summary. """,
    description="Researches renewable energy sources.",
    tools=[google_search],
    # Store result in state for the merger agent
    output_key="renewable_energy_result",
)

# Researcher 2: Electric Vehicles
researcher_agent_2 = LlmAgent(
    name="EVResearcher",
    model=GEMINI_MODEL,
    instruction="""You are an AI Research Assistant specializing in transportation. Research the latest developments in 'electric vehicle technology'. Use the Google Search tool provided. Summarize your key findings concisely (1-2 sentences). Output *only* the summary. """,
    description="Researches electric vehicle technology.",
    tools=[google_search],
    # Store result in state for the merger agent
    output_key="ev_technology_result",
)

# Researcher 3: Carbon Capture
researcher_agent_3 = LlmAgent(
    name="CarbonCaptureResearcher",
    model=GEMINI_MODEL,
    instruction="""You are an AI Research Assistant specializing in climate solutions. Research the current state of 'carbon capture methods'. Use the Google Search tool provided. Summarize your key findings concisely (1-2 sentences). Output *only* the summary. """,
    description="Researches carbon capture methods.",
    tools=[google_search],
    # Store result in state for the merger agent
    output_key="carbon_capture_result",
)


# --- 2. Create the ParallelAgent (Runs researchers concurrently) ---
# This agent orchestrates the concurrent execution of the researchers.
# It finishes once all researchers have completed and stored their results in state.
parallel_research_agent = ParallelAgent(
    name="ParallelWebResearchAgent",
    sub_agents=[researcher_agent_1, researcher_agent_2, researcher_agent_3],
    description="Runs multiple research agents in parallel to gather information.",
)


# --- 3. Define the Merger Agent (Runs after the parallel agents) ---
# This agent takes the results stored in the session state by the parallel agents
# and synthesizes them into a single, structured response with attributions.
merger_agent = LlmAgent(
    name="SynthesisAgent",
    model=GEMINI_MODEL,  # Or potentially a more powerful model if needed for synthesis
    instruction="""You are an AI Assistant responsible for combining research findings into a structured report. Your primary task is to synthesize the following research summaries, clearly attributing findings to their source areas. Structure your response using headings for each topic. Ensure the report is coherent and integrates the key points smoothly.

**Crucially:** Your entire response MUST be grounded *exclusively* on the information provided in the 'Input Summaries' below. Do NOT add any external knowledge, facts, or details not present in these specific summaries.

**Input Summaries:**
*   **Renewable Energy:**
    {renewable_energy_result}
*   **Electric Vehicles:**
    {ev_technology_result}
*   **Carbon Capture:**
    {carbon_capture_result}

**Output Format:**
## Summary of Recent Sustainable Technology Advancements

### Renewable Energy Findings (Based on RenewableEnergyResearcher's findings)
[Synthesize and elaborate *only* on the renewable energy input summary provided above.]

### Electric Vehicle Findings (Based on EVResearcher's findings)
[Synthesize and elaborate *only* on the EV input summary provided above.]

### Carbon Capture Findings (Based on CarbonCaptureResearcher's findings)
[Synthesize and elaborate *only* on the carbon capture input summary provided above.]

### Overall Conclusion
[Provide a brief (1-2 sentence) concluding statement that connects *only* the findings presented above.]

Output *only* the structured report following this format. Do not include introductory or concluding phrases outside this structure, and strictly adhere to using only the provided input summary content.
""",
    description="Combines research findings from parallel agents into a structured, cited report, strictly grounded on provided inputs.",
    # No tools needed for merging
    # No output_key needed here, as its direct response is the final output of the sequence
)

# --- 4. Create the SequentialAgent (Orchestrates the overall flow) ---
# This is the main agent that will be run. It first executes the ParallelAgent
# to populate the state, and then executes the MergerAgent to produce the final output.
sequential_pipeline_agent = SequentialAgent(
    name="ResearchAndSynthesisPipeline",
    # Run parallel research first, then merge
    sub_agents=[parallel_research_agent, merger_agent],
    description="Coordinates parallel research and synthesizes the results.",
)

root_agent = sequential_pipeline_agent
```

上述程式碼定義了一套多代理系統，用於研究並綜整永續科技進展。它建立三個 LlmAgent（LlmAgent）實例作為專業研究員：`ResearcherAgent_1`（ResearcherAgent_1）專注可再生能源來源，`ResearcherAgent_2`（ResearcherAgent_2）研究電動車技術，而 `ResearcherAgent_3`（ResearcherAgent_3）調查碳捕捉方法。每個研究員代理都設定使用 GEMINI 模型（GEMINI_MODEL）與 google_search 工具（google_search tool），被指示以一至兩句的精簡摘要回報重點，並透過 `output_key`（output_key）將摘要儲存於工作階段狀態。

接著建立名為 ParallelWebResearchAgent（ParallelWebResearchAgent）的 ParallelAgent（ParallelAgent），以並行方式執行三個研究員代理，讓研究能同步進行並節省時間。ParallelAgent（ParallelAgent）會在所有子代理完成並將結果寫入狀態後結束執行。

之後定義 MergerAgent（MergerAgent），同樣為 LlmAgent（LlmAgent），用以綜合研究成果。此代理從並行研究員寫入工作階段狀態的摘要作為輸入，指令強調輸出必須嚴格根據提供的摘要，禁止加入外部知識。MergerAgent（MergerAgent）旨在將整合結果以每個主題的標題呈現，並提供簡短總結。

最後，建立名為 ResearchAndSynthesisPipeline（ResearchAndSynthesisPipeline）的 SequentialAgent（SequentialAgent）來統籌整個工作流程。此主控代理會先執行 ParallelAgent（ParallelAgent）進行研究，待並行研究完成後，再執行 MergerAgent（MergerAgent）整合資訊。`sequential_pipeline_agent`（sequential_pipeline_agent）被設定為 `root_agent`（root_agent），作為運行這套多代理系統的入口。整體流程旨在有效率地並行蒐集多方資訊，然後整合成單一結構化報告。

## 重點一覽(At a Glance)

**重點：** 許多代理工作流程包含必須完成多個子任務才能達成最終目標。純粹的順序執行（sequential execution），即每個任務都等待前一個完成，往往效率低且耗時。當任務仰賴外部輸入／輸出作業（例如呼叫不同應用程式介面或查詢多個資料庫）時，這種延遲更成為顯著瓶頸。如果缺乏併發執行機制，總處理時間就是全部個別任務時間的總和，阻礙系統整體效能與回應力。

**原因：** 並行化（Parallelization）模式提供標準化的解決方案，能夠同時執行相互獨立的任務。它的運作方式是辨識工作流程中不依賴彼此即時輸出的元件，例如工具使用或大型語言模型呼叫。像 LangChain 框架（LangChain）與 Google 代理開發套件（Google ADK）等代理框架提供內建結構，用以定義並管理這些併發操作。例如，主流程可以啟動多個並行子任務，並在全部完成後再進行下一步。透過同時執行獨立任務而非依序處理，此模式能大幅縮短整體執行時間。

**經驗法則：** 當工作流程包含多個可以同時運行的獨立操作時，應採用此模式，例如從多個應用程式介面擷取資料、處理不同資料區塊，或生成多項內容以供後續綜整。

**視覺摘要：**

![並行化設計模式（Parallelization Design Pattern）](../assets/Parallelization_Design_Pattern.png)

圖二：並行化設計模式

## 重要重點（Key Takeaways）

以下為關鍵重點：

* 並行化（Parallelization）是用於同時執行獨立任務以提升效率的模式。
* 當任務需要等待外部資源（例如應用程式介面呼叫）時，此模式特別有用。
* 採用併發或平行架構會引入相當的複雜度與成本，影響設計、除錯與系統記錄等開發關鍵階段。
* 像 LangChain 框架（LangChain）與 Google 代理開發套件（Google ADK）等框架提供內建支援，以定義並管理並行執行。
* 在 LangChain 表達語言（LangChain Expression Language，LCEL）中，RunnableParallel（RunnableParallel）是並行執行多個可執行單元的核心構件。
* Google 代理開發套件（Google ADK）可透過大型語言模型驅動的委派（LLM-Driven Delegation），由協調者代理（Coordinator agent）的大型語言模型辨識獨立子任務，並觸發專門子代理的並行處理。
* 並行化（Parallelization）有助於降低整體延遲，讓代理系統在處理複雜任務時更為靈敏。

## 結論（Conclusion）

並行化（Parallelization）模式是一種藉由同時執行獨立子任務以最佳化計算工作流程的方法。此作法能降低整體延遲，尤其適用於包含多次模型推論或外部服務呼叫的複雜操作。

各種框架提供不同的實作機制。在 LangChain 框架（LangChain）中，可使用 RunnableParallel（RunnableParallel）等構件明確定義並同時執行多個處理鏈結。相對地，像 Google Agent Developer Kit（Google Agent Developer Kit，ADK）等框架可透過多代理委派（multi-agent delegation）達成並行化，由主要協調代理分派不同子任務給能同步運作的專門代理。

將並行處理與順序（鏈結）（sequential chaining）及條件（導向）（conditional routing）控制流程整合後，即可建構高度複雜且高效能的計算系統，有效管理多樣且複雜的任務。

## References

Here are some resources for further reading on the Parallelization pattern and related concepts:

1. LangChain Expression Language (LCEL) Documentation (Parallelism): [https://python.langchain.com/docs/concepts/lcel/](https://python.langchain.com/docs/concepts/lcel/)
2. Google Agent Developer Kit (ADK) Documentation (Multi-Agent Systems): [https://google.github.io/adk-docs/agents/multi-agents/](https://google.github.io/adk-docs/agents/multi-agents/)
3. Python `asyncio` Documentation: [https://docs.python.org/3/library/asyncio.html](https://docs.python.org/3/library/asyncio.html)
