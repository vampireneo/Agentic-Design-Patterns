# 第六章：規劃（Planning）

智能行為（Intelligent behavior）往往不只是對即時輸入作出反應。它需要前瞻性，將複雜任務拆解為較小且可管理的步驟，並策劃（strategizing）如何達成期望結果。這正是規劃（Planning）模式（pattern）的用武之地。規劃的核心，是讓一個代理（agent）或一個代理系統（system of agents）構思一連串行動，從初始狀態（initial state）推進至目標狀態（goal state）。

## 規劃模式概覽（Planning Pattern Overview）

在人工智能（AI）的語境下，可以把規劃代理（planning agent）想像成一位專家，你把複雜目標委派給它處理。當你要求它「安排一次團隊外出活動」，你定義的是「做甚麼」（what）——目標與約束條件——而不是「如何做」（how）。代理的核心任務，是自動繪製前往目標的路線。它必須先理解初始狀態（例如預算、參與人數、期望日期）與目標狀態（成功預訂外出活動），然後找出連接兩者的最佳行動序列。這份計劃並非預先得知，而是對需求作出回應時才被創建。

這個過程的一大特徵是適應力。初步計劃只是起點，而不是僵化的腳本。代理真正的強大之處，是能吸收新資訊並帶領專案避開障礙。例如，若首選場地無法使用或指定餐飲已經滿額，一個有能力的代理不會就此失敗。它會調整，記錄新約束，重新評估選項並制定新計劃，可能改提替代場地或日期。

然而，必須認清靈活性與可預測性之間的取捨。動態規劃（dynamic planning）是一種特定工具，而非萬能解方。當問題的解決方法已經明確且可重複時，將代理限制在預先定義、固定的工作流程會更有效。這種方法限制代理自主權，以降低不確定性及不可預測行為的風險，從而保證可靠一致的結果。因此，選擇使用規劃代理還是簡單任務執行代理，取決於一個核心問題：這個「如何做」需要被發掘，還是早已為人所知？

## 實際應用與使用案例（Practical Applications & Use Cases）

規劃模式是自治系統（autonomous systems）的核心運算過程，讓代理能綜合出一系列行動，以達成指定目標，尤其適用於動態或複雜環境。這個過程將高層次目標轉化為由離散且可執行步驟組成的結構化計劃。

在程序化任務自動化（procedural task automation）等領域，規劃被用來協調複雜工作流程。例如，像是新員工入職這類商業流程，可以被分解成有方向性的子任務序列，例如建立系統帳戶、分配培訓課程，以及與不同部門協調。代理會生成計劃，以合乎邏輯的順序執行這些步驟，調用必要的工具或與各個系統互動，以管理依賴關係。

在機器人學（robotics）與自主導航（autonomous navigation）中，規劃對於狀態空間遍歷（state-space traversal）至關重要。無論是實體機器人或虛擬實體，都必須生成路徑或行動序列，從初始狀態通往目標狀態。這涉及在遵守環境約束（例如避開障礙或遵守交通法規）的前提下，優化時間或能源消耗等指標。

這個模式同樣對結構化資訊綜合（structured information synthesis）十分重要。當代理被要求產生像研究報告這樣的複雜輸出時，它可以制定包含明確階段的計劃，例如資訊收集、數據摘要、內容結構化與反覆修訂。類似地，在涉及多步驟問題解決的客戶支援情境中，代理可以創建並遵循系統化計劃，進行診斷、實施方案及必要時升級處理。

總括而言，規劃模式讓代理超越單純的反應式行為，轉而實現以目標為導向的運作。它提供了解決需要互相依存操作的問題所需的邏輯框架。

## 實作示範：Crew AI（Hands-on code (Crew AI)）

以下章節會示範如何利用 Crew AI 框架（framework）實作規劃者（Planner）模式。這個模式涉及代理先為複雜查詢制定多步驟計劃，然後按順序執行計劃。

```python
import os
from dotenv import load_dotenv
from crewai import Agent, Task, Crew, Process
from langchain_openai import ChatOpenAI


# Load environment variables from .env file for security
load_dotenv()


# 1. Explicitly define the language model for clarity
llm = ChatOpenAI(model="gpt-4-turbo")


# 2. Define a clear and focused agent
planner_writer_agent = Agent(
    role='Article Planner and Writer',
    goal='Plan and then write a concise, engaging summary on a specified topic.',
    backstory=(
        'You are an expert technical writer and content strategist. '
        'Your strength lies in creating a clear, actionable plan before writing, '
        'ensuring the final summary is both informative and easy to digest.'
    ),
    verbose=True,
    allow_delegation=False,
    llm=llm,  # Assign the specific LLM to the agent
)


# 3. Define a task with a more structured and specific expected output
topic = "The importance of Reinforcement Learning in AI"

high_level_task = Task(
    description=(
        f"1. Create a bullet-point plan for a summary on the topic: '{topic}'.\n"
        f"2. Write the summary based on your plan, keeping it around 200 words."
    ),
    expected_output=(
        "A final report containing two distinct sections:\n\n"
        "### Plan\n"
        "- A bulleted list outlining the main points of the summary.\n\n"
        "### Summary\n"
        "- A concise and well-structured summary of the topic."
    ),
    agent=planner_writer_agent,
)


# Create the crew with a clear process
crew = Crew(
    agents=[planner_writer_agent],
    tasks=[high_level_task],
    process=Process.sequential,
)


# Execute the task
print("## Running the planning and writing task ##")
result = crew.kickoff()

print("\n\n---\n## Task Result ##\n---")
print(result)
```

這段程式碼使用 CrewAI 程式庫（library）建立一個會規劃並撰寫摘要的人工智能代理（AI agent）。它先匯入必要的程式庫，包括 Crew AI 及 `langchain_openai`，並從 .env 檔案載入環境變數。程式明確定義將被代理使用的 ChatOpenAI 語言模型（language model）。名稱為 `planner_writer_agent` 的代理被建立，擁有特定角色與目標：在撰寫前先規劃，再產出精簡摘要。代理的背景故事強調其在規劃與技術寫作上的專長。任務（Task）則被定義得清晰，要求先為題為「人工智能中強化學習的重要性」的主題制訂計劃，再根據計劃撰寫約 200 字的摘要，並提供具體的輸出格式。最後，建立一個以順序流程（sequential process）執行任務的 Crew，並呼叫 crew.kickoff() 來執行任務，再輸出結果。

## Google Gemini DeepResearch

Google Gemini DeepResearch（見圖 1）是一套為自主資訊檢索與綜合而設計的代理式系統（agent-based system）。它透過多步驟代理流程（agentic pipeline），動態且反覆地查詢 Google 搜尋，以系統化方式探索複雜主題。這套系統能處理大量網路來源，評估所收集的資料是否相關、是否存在知識缺口，並進一步搜尋以填補缺口。最終輸出則將篩選過的資訊整合為結構化的多頁摘要，並附上原始來源的引文。

進一步來說，這套系統的運作並非單一的查詢回應事件，而是一個受管理的長時程流程。它先將使用者的提示（prompt）拆解成多點研究計劃（見圖 1），然後呈交給使用者審閱與修改，讓雙方可在執行前共同塑造研究路徑。計劃獲批後，代理流程會啟動其反覆的搜尋與分析迴圈。這不只是執行一連串預先定義的搜尋，而是代理會根據蒐集到的資訊動態地擬定與調整查詢，主動辨識知識缺口、互相印證資料點並解決矛盾。

![Google Deep Research agent generating an execution plan for using Google Search as a tool](../assets/Google_Deep_Research_Agent_Generating_an_execution_plan_for_using_Google_Search_as_a_Tool.png)

圖 1：Google Deep Research 代理為使用 Google 搜尋作工具而生成執行計劃。

其關鍵架構組件之一，是系統能以非同步（asynchronous）方式管理流程。這項設計確保探索過程能處理上百個來源時仍具備對單點失效（single-point failures）的韌性，同時允許使用者在執行期間暫時離線，並在完成時收到通知。系統亦可整合使用者提供的文件，把私人來源與網路研究結合。最終輸出並非單純把結果串接，而是一份結構化、多頁的報告。在綜合階段，模型會對收集到的資訊進行批判性評估，識別主要主題，並把內容組織成具有邏輯分段的敘事。報告亦會被設計為具互動性，常見功能包括語音總覽、圖表及指向原始引用來源的連結，讓使用者得以驗證與延伸探索。除了綜合結果外，模型還會明確返回其搜尋與查閱過的完整來源清單（見圖 2），以引用形式呈現，確保完全透明，並提供直接存取原始資訊的途徑。整個流程把簡單查詢轉化為全面且具綜合性的知識體。

![An example of Deep Research plan being executed, resulting in Google Search being used as a tool to search various web sources](../assets/Example_of_Deep_Research_Plan_Being_Executed_Resulting_in_Google_Search_being_used_as_a_Tool_to_Search_Various_Web_Sources.png)

圖 2：Deep Research 計劃的執行範例，顯示如何使用 Google 搜尋作為工具去搜尋多個網絡來源。

透過減少手動資料搜集與綜合所需的大量時間與資源，Gemini DeepResearch 提供了一種更具結構且徹底的資訊發現方法。這套系統的價值在多領域的複雜、多面向研究任務中特別明顯。

舉例來說，在競爭分析（competitive analysis）中，可以指示代理系統化地收集與整理市場趨勢、競爭對手產品規格、從不同線上來源獲得的大眾情緒，以及行銷策略。這個自動化流程取代了人工追蹤多個競爭對手的繁瑣工作，讓分析師可以專注於更高層次的策略解讀，而非資料蒐集（見圖 3）。

![Final output generated by the Google Deep Research agent, analyzing on our behalf sources obtained using Google Search as a tool](../assets/Final_Output_Generated_by_Google_Deep_Research_Agent_Analyzing_on_our_Behalf_Sources_Obtained_using_Google_Search_as_a_Tool.png)

圖 3：Google Deep Research 代理為我們分析使用 Google 搜尋取得之來源後生成的最終輸出。

同樣地，在學術探索（academic exploration）中，該系統是進行廣泛文獻回顧的強大工具。它能識別及總結基礎論文，追蹤概念在多篇出版物中的發展，並描繪特定領域的新興研究前沿，從而加速學術研究最初且最耗時的階段。

這種方法的效率來自於自動化迭代搜尋與篩選循環，這是手動研究的核心瓶頸。系統能處理的資訊量與多樣性也遠超人類研究者在相同時間內所能達成，從而提升全面性。更廣泛的分析範圍有助減少選擇偏差（selection bias），並提高發現不那麼顯眼但可能關鍵資訊的機率，最終帶來更全面且有力支持的主題理解。

## OpenAI Deep Research API

OpenAI Deep Research API 是一款專門設計來自動化複雜研究任務的工具。它運用先進的代理式模型（agentic model），能獨立推理、規劃並從真實世界來源綜合資訊。與單純的問答（Q&A）模型不同，它會接收高層次查詢，自主拆解為子問題，透過內建工具進行網頁搜尋，並產出結構化且充滿引用的最終報告。這個 API 讓開發者可以以程式方式直接存取整個流程，並在撰寫時提供像 o3-deep-research-2025-06-26 這種高品質綜合模型，以及對延遲敏感應用可使用的 o4-mini-deep-research-2025-06-26。

Deep Research API 的價值，在於它能自動完成原本需耗費數小時的手動研究，交付具專業水準、以數據支援的報告，可用於指導商業策略、投資決策或政策建議。其主要優點包括：

* **結構化且具引用的輸出（Structured, Cited Output）：** 產出組織良好的報告，附上連結至來源中繼資料（metadata）的行內引用，確保所有主張均可查證並有數據支持。
* **透明度（Transparency）：** 與在 ChatGPT 中較為抽象的流程不同，這個 API 會公開所有中間步驟，包括代理的推理（reasoning）、執行的具體網頁搜尋查詢，以及任何運行過的程式碼。這讓詳細除錯、分析與理解最終答案如何構建成為可能。
* **可擴展性（Extensibility）：** 支援模型上下文協定（Model Context Protocol, MCP），讓開發者可把代理連接至私人知識庫及內部資料來源，將公共網路研究與專有資訊融合。

要使用這個 API，你需要向 client.responses.create 端點送出請求，指定模型、輸入提示（prompt）與代理可使用的工具。輸入內容通常包括定義代理人格與期望輸出格式的 `system_message`，以及 `user_query`。你亦必須包含 `web_search_preview` 工具，並可選擇加入 `code_interpreter` 或自訂的 MCP 工具（詳見第十章）以存取內部資料。

```python
from openai import OpenAI


# Initialize the client with your API key
client = OpenAI(api_key="YOUR_OPENAI_API_KEY")


# Define the agent's role and the user's research question
system_message = """
You are a professional researcher preparing a structured, data-driven report.
Focus on data-rich insights, use reliable sources, and include inline citations.
"""

user_query = "Research the economic impact of semaglutide on global healthcare systems."


# Create the Deep Research API call
response = client.responses.create(
    model="o3-deep-research-2025-06-26",
    input=[
        {
            "role": "developer",
            "content": [{"type": "input_text", "text": system_message}],
        },
        {
            "role": "user",
            "content": [{"type": "input_text", "text": user_query}],
        },
    ],
    reasoning={"summary": "auto"},
    tools=[{"type": "web_search_preview"}],
)


# Access and print the final report from the response
final_report = response.output[-1].content[0].text
print(final_report)


# --- ACCESS INLINE CITATIONS AND METADATA ---
print("--- CITATIONS ---")
annotations = response.output[-1].content[0].annotations

if not annotations:
    print("No annotations found in the report.")
else:
    for i, citation in enumerate(annotations):
        # The text span the citation refers to
        cited_text = final_report[citation.start_index : citation.end_index]
        print(f"Citation {i + 1}:")
        print(f"  Cited Text: {cited_text}")
        print(f"  Title: {citation.title}")
        print(f"  URL: {citation.url}")
        print(f"  Location: chars {citation.start_index}–{citation.end_index}")

print("\n" + "=" * 50 + "\n")


# --- INSPECT INTERMEDIATE STEPS ---
print("--- INTERMEDIATE STEPS ---")

# 1. Reasoning Steps: Internal plans and summaries generated by the model.
try:
    reasoning_step = next(item for item in response.output if item.type == "reasoning")
    print("\n[Found a Reasoning Step]")
    for summary_part in reasoning_step.summary:
        print(f"  - {summary_part.text}")
except StopIteration:
    print("\nNo reasoning steps found.")

# 2. Web Search Calls: The exact search queries the agent executed.
try:
    search_step = next(item for item in response.output if item.type == "web_search_call")
    print("\n[Found a Web Search Call]")
    print(f"  Query Executed: '{search_step.action['query']}'")
    print(f"  Status: {search_step.status}")
except StopIteration:
    print("\nNo web search steps found.")

# 3. Code Execution: Any code run by the agent using the code interpreter.
try:
    code_step = next(item for item in response.output if item.type == "code_interpreter_call")
    print("\n[Found a Code Execution Step]")
    print("  Code Input:")
    print(f"  ```python\n{code_step.input}\n  ```")
    print("  Code Output:")
    print(f"  {code_step.output}")
except StopIteration:
    print("\nNo code execution steps found.")
```

這段程式碼利用 OpenAI API 執行「Deep Research」任務。它先以 API 金鑰初始化 OpenAI 用戶端（client），這對認證至關重要。接著，程式為人工智能代理定義角色為專業研究員，並設定使用者提出的關於 semaglutide 對全球醫療體系經濟影響的研究問題。程式建立對 o3-deep-research-2025-06-26 模型的 API 呼叫，提供預先定義的 system_message 與 user_query 作為輸入，同時請求推理摘要並啟用網頁搜尋工具。呼叫完成後，程式會擷取並輸出生成的最終報告。

其後，程式嘗試存取並顯示報告中的行內引用與中繼資料，包括被引用的文字、標題、網址與在報告內的位置。最後，程式會檢視並輸出模型在過程中執行的中介步驟，例如推理步驟、網頁搜尋呼叫（含具體查詢）以及若使用程式碼解譯器（code interpreter）時所執行的任何程式碼。

## 重點一覽(At a Glance)

**重點（What）：** 複雜問題往往無法以單一步驟解決，需要前瞻性才能達成期望結果。若缺乏結構化方法，代理系統將難以處理涉及多個步驟與依賴關係的複合需求，難以把高層次目標拆解成可管理的一系列較小且可執行的任務。因此，當面對複雜目標時，系統容易因缺乏策略而得出不完整或不正確的結果。

**原因（Why）：** 規劃模式提供一套標準化解方，要求代理系統先為目標建立連貫計劃。它會把高層次目標拆解成一系列較小且可行的步驟或子目標，讓系統能以合乎邏輯的順序管理複雜工作流程、調度多種工具並處理依賴關係。大型語言模型（LLMs）特別擅長這方面，因為它們可根據龐大的訓練資料生成合理而有效的計劃。透過這種結構化方式，系統從單純的反應式代理轉變為策略執行者，甚至能在必要時調整計劃。

**經驗法則（Rule of thumb）：** 當使用者的需求過於複雜，無法以單一行動或工具完成時，就應使用這個模式。它適用於自動化多步驟流程，例如產出詳盡的研究報告、新員工入職或執行競爭分析。凡是需要一連串互相依存的操作才能達成最終綜合成果的任務，都可以套用規劃模式。

**視覺摘要（Visual summary）**

![Planning Design Pattern](../assets/Planning_Design_Pattern.png)

圖 4：規劃設計模式。

## 重要要點（Key Takeaways）

* 規劃讓代理得以把複雜目標拆解成可執行且具順序的步驟。
* 它對處理多步驟任務、工作流程自動化及導覽複雜環境至關重要。
* 大型語言模型能透過生成逐步方法來執行規劃。
* 明確提示或設計需要規劃步驟的任務，可在代理框架（agent frameworks）中促進這種行為。
* Google Deep Research 是一個為我們分析使用 Google 搜尋取得之來源的代理，具備反思（reflection）、規劃與執行能力。

## 結論（Conclusion）

總結而言，規劃模式是讓代理系統從單純的反應式回應者，提升為策略型、目標導向執行者的基礎元件。現代大型語言模型提供了核心能力，能自動將高層次目標拆解成連貫且可執行的步驟。這個模式的範圍可從簡單、具順序的任務執行（例如 CrewAI 代理創建並遵循寫作計劃）擴展至更複雜且動態的系統。Google DeepResearch 代理正是這種進階應用的典範，它建立會隨資訊持續收集而調整與演化的迭代研究計劃。最終，規劃為人類意圖與自動化執行之間搭建了關鍵橋樑。透過結構化的解題方式，這個模式讓代理能掌握繁複工作流程，並交付全面且整合的成果。

## References

1. Google DeepResearch (Gemini Feature): [gemini.google.com](http://gemini.google.com)
2. OpenAI ,Introducing deep research  [https://openai.com/index/introducing-deep-research/](https://openai.com/index/introducing-deep-research/)
3. Perplexity, Introducing Perplexity Deep Research, [https://www.perplexity.ai/hub/blog/introducing-perplexity-deep-research](https://www.perplexity.ai/hub/blog/introducing-perplexity-deep-research)
