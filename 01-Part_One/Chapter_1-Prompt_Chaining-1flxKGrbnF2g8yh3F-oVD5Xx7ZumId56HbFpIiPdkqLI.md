# 第1章：提示鏈結(Prompt chaining)

## 提示鏈結(Prompt chaining)模式概覽

提示鏈結(Prompt chaining)，有時亦稱為管道模式(Pipeline pattern)，代表在運用大型語言模型(Large Language Models, LLMs)處理複雜任務時的一種強而有力的範式(paradigm)。與其期望單一大型語言模型(Large Language Model, LLM)在一個龐大的步驟中解決複雜問題，提示鏈結(Prompt chaining)主張採用分而治之(divide-and-conquer)策略。核心理念是把原本令人生畏的問題分拆為一連串較小、較易管理的子問題。每個子問題都透過特別設計的提示(prompt)獨立處理，而某一提示(prompt)產生的輸出會策略性地餵入鏈上下一個提示(prompt)作為輸入。

這種順序處理的技術天然為與大型語言模型(Large Language Models, LLMs)的互動帶來模組化(modularity)與清晰度(clarity)。透過分解複雜任務，更容易理解與除錯每一個獨立步驟，使整體流程更堅固且可解釋(interpretable)。鏈上每一步都能被細緻地設計與優化，以專注於較大問題中的特定面向，從而產生更精準且聚焦的輸出。

其中一個步驟的輸出作為下一步驟輸入的特性至關重要。這種資訊傳遞建立起依存鏈(dependency chain)，故得其名，過往操作的脈絡(context)與結果會指引後續的處理。這種設計讓大型語言模型(Large Language Model, LLM)得以在既有成果上累積、細化理解，並逐步接近期望的解決方案。

此外，提示鏈結(Prompt chaining)不僅涉及問題的拆解，亦使得整合外部知識與工具成為可能。在每一個步驟，大型語言模型(Large Language Model, LLM)都可以被指示與外部系統、應用程式介面(APIs)或資料庫(databases)互動，從而超越其內部訓練資料(training data)的界限。這種能力大幅擴展大型語言模型(Large Language Models, LLMs)的潛力，讓它們不再只是孤立的模型，而能成為更廣泛、更智能系統中的核心組件。

提示鏈結(Prompt chaining)的重要性超越一般問題解決，亦是建構精密AI代理人(AI agents)的基礎技術。這些代理人(agents)可以運用提示鏈(prompt chains)在動態環境中自動規劃、推理與行動。透過策略性地安排提示(prompt)的序列，代理人(agent)能夠處理需要多步推理(multi-step reasoning)、規劃(planning)與決策(decision-making)的任務。此類代理工作流程能更貼近模仿人類的思考過程，令與複雜領域與系統的互動更自然、更有效。

**單一提示(prompt)的限制：** 面對多面向任務時，使用單一且複雜的提示(prompt)往往效率低下，令大型語言模型(Large Language Model, LLM)在約束(constraints)與指示(instructions)之間掙扎，並可能出現指示忽略(instruction neglect)（提示(prompt)部分被遺漏）、脈絡漂移(contextual drift)（模型失去初始脈絡）、錯誤傳播(error propagation)（早期錯誤被放大）、需要更長脈絡窗(context window)而資訊不足，以及幻覺(hallucination)（認知負荷增高導致錯誤資訊）。例如，一個要求分析市場研究報告、摘要發現、列出具數據指標的趨勢並草擬電郵的查詢很可能失敗，因為模型或許能妥善摘要，但無法正確提取數據或撰寫電郵。

**透過序列分解提升可靠性：** 提示鏈結(Prompt chaining)把複雜任務拆解為聚焦的順序工作流程，從而大幅提升可靠性(reliability)與控制力(control)。以以上例子為例，可以如下描述一個管道或鏈式流程：

1. 初始提示(Prompt)（摘要）：「請摘要以下市場研究報告的重點：\[text\]。」模型的唯一焦點是摘要，提升此初始步驟的準確度。
2. 第二個提示(Prompt)（趨勢識別）：「基於摘要，指出三個最重要的新興趨勢，並提取支持每個趨勢的具體數據點：\[output from step 1\]。」此提示(prompt)更聚焦，直接建立在已驗證的輸出上。
3. 第三個提示(Prompt)（電郵撰寫）：「撰寫一封簡潔電郵給市場團隊，概述上述趨勢及其支援數據：\[output from step 2\]。」

這種分解為流程帶來更細緻的控制。每一步更簡單、更少含糊，能降低模型的認知負荷，並帶來更準確可靠的最終輸出。此種模組化設計類似於計算管線(computational pipeline)，每個函式(function)在將結果傳給下一個函式前執行特定操作。為確保每項任務獲得準確回應，模型可以在每個階段被賦予特定角色。例如，在此情境中，初始提示(prompt)可被指定為「市場分析師(Market Analyst)」，下一個提示(prompt)為「交易分析師(Trade Analyst)」，第三個提示(prompt)為「專業文檔撰寫者(Expert Documentation Writer)」等。

**結構化輸出的角色：** 提示鏈(prompt chain)的可靠性極度依賴步驟之間所傳遞資料的完整性。如果某個提示(prompt)的輸出含糊或格式不佳，後續提示(prompt)可能因輸入有誤而失敗。為減輕此風險，指定結構化輸出格式（例如JSON或XML）至關重要。

例如，趨勢識別步驟的輸出可以格式化為JSON物件(JSON object)：

```json
{  "trends": [
        {
            "trend_name": "AI-Powered Personalization",
            "supporting_data": "73% of consumers prefer to do business with brands that use personal information to make their shopping experiences more relevant."
        },
        {
            "trend_name": "Sustainable and Ethical Brands",
            "supporting_data": "Sales of products with ESG-related claims grew 28% over the last five years, compared to 20% for products without."
        }
    ]
}
```

這種結構化格式確保資料可供機器讀取(machine-readable)，並且可以精準地解析(parse)及無歧義地插入下一個提示(prompt)。此做法把因自然語言解讀而可能出現的錯誤降至最低，是建構穩健多步驟大型語言模型(Large Language Model, LLM)系統的關鍵元素。

## 實務應用與使用案例

在建構代理式系統(agentic systems)時，提示鏈結(Prompt chaining)是一種適用於各種情境的靈活模式(pattern)。其核心效用在於把複雜問題拆解為連續且可管理的步驟。以下是若干實務應用與使用案例：

### 1. 資訊處理工作流程

許多任務需要透過多重轉換(processing transformations)處理原始資訊。例如，摘要文件、提取關鍵實體(entities)，再使用這些實體去查詢資料庫(database)或產生報告(report)。一個提示鏈(prompt chain)可以如下：

* 提示(Prompt)1：從指定的網址(URL)或文件(document)提取文字內容。
* 提示(Prompt)2：摘要清理後的文字。
* 提示(Prompt)3：從摘要或原文中提取特定實體(entities)（例如名稱、日期、地點）。
* 提示(Prompt)4：使用這些實體(entities)搜尋內部知識庫(knowledge base)。
* 提示(Prompt)5：整合摘要、實體(entities)與搜尋結果，生成最終報告(report)。

此方法運用於自動化內容分析(automated content analysis)、AI驅動的研究助理(research assistants)開發，以及複雜報告產生(complex report generation)等領域。

### 2. 複雜查詢回應

回答需要多步推理(multi-step reasoning)或資訊檢索(information retrieval)的複雜問題是提示鏈結(Prompt chaining)的主要使用案例之一。例如：「1929年股災的主要成因為何，而政府政策如何回應？」

* 提示(Prompt)1：識別使用者查詢中的核心子問題（股災成因、政府回應）。
* 提示(Prompt)2：研究或檢索與1929年股災成因相關的資訊。
* 提示(Prompt)3：研究或檢索與政府針對1929年股災的政策回應相關的資訊。
* 提示(Prompt)4：把第2與第3步的資訊綜合，形成對原始查詢的連貫答案。

這種順序處理法是發展能進行多步推理與資訊綜整(information synthesis)的AI系統的核心。當查詢無法由單一資料點回答，而是需要一系列邏輯步驟或多元資訊整合時，便需要此類系統。

例如，一個自動化研究代理人(automated research agent)旨在生成針對特定主題的完整報告(report)，會執行混合式計算流程(hybrid computational workflow)。系統首先檢索大量相關文章。接下來的任務是從每篇文章中提取關鍵資訊，此步驟可對每個來源並行執行，十分適合平行處理(parallel processing)，同時運行獨立子任務以最大化效率。

...

### 3. 數據提取與轉換

把非結構化文字(unstructured text)轉換為結構化格式(structured format)通常透過迭代程序(iterative process)達成，需要連續調整以提升輸出的準確性與完整性。

* 提示(Prompt)1：嘗試從發票文件(invoice document)提取特定欄位（例如名稱、地址、金額）。
* 處理(Processing)：檢查是否所有必需欄位均已提取且符合格式要求。
* 提示(Prompt)2（有條件）：若欄位遺漏或格式錯誤，則撰寫新提示(prompt)，要求模型具體找出遺漏或不正確的資訊，並可能提供失敗嘗試的脈絡。
* 處理(Processing)：再次驗證結果。如有需要重複進行。
* 輸出(Output)：提供提取且驗證後的結構化資料(structured data)。

這種順序處理特別適用於從非結構化來源（例如表格、發票、電郵）提取與分析資料。例如，解決複雜的光學字元辨識(Optical Character Recognition, OCR)問題，如處理PDF表格，更適合透過拆解、多步驟方式處理。

首先，使用大型語言模型(Large Language Model, LLM)從文件影像(document image)進行主要文字提取。隨後，模型處理原始輸出以正規化(normalize)資料，例如把「one thousand and fifty」等數字文字轉換為其數值等值1050。由於大型語言模型(Large Language Models, LLMs)在執行精確數學計算(mathematical calculations)方面面臨重大挑戰，因此在接下來的步驟中，系統可把所需算術運算(arithmetic operations)委派給外部計算工具(external calculator tool)。大型語言模型(Large Language Model, LLM)識別所需計算，將正規化後的數字傳給工具，然後整合精確結果。這條由文字提取、資料正規化與外部工具使用構成的鏈式序列，使得最終精確結果得以實現，而這往往難以透過單一大型語言模型(Large Language Model, LLM)查詢可靠獲得。

### 4. 內容生成工作流程

複雜內容的創作是一個程序化任務(process-driven task)，通常被拆解為明確階段，包括初步構思(ideation)、架構大綱(structural outlining)、草稿撰寫(drafting)與後續修訂(revision)。

* 提示(Prompt)1：根據使用者的整體興趣產生五個主題構想(topic ideas)。
* 處理(Processing)：讓使用者選擇其中一個構想，或自動挑選最佳選項。
* 提示(Prompt)2：根據所選主題產生詳細大綱(outline)。
* 提示(Prompt)3：根據大綱第一點撰寫草稿段落(draft section)。
* 提示(Prompt)4：根據大綱第二點撰寫草稿段落(draft section)，並提供前一段作為脈絡。對所有大綱要點重複進行。
* 提示(Prompt)5：檢視並完善整份草稿，確保連貫性(coherence)、語氣(tone)與語法(grammar)。

此方法被應用於多種自然語言生成(natural language generation)任務，包括自動撰寫創意敘事(creative narratives)、技術文件(technical documentation)及其他形式的結構化文字內容(structured textual content)。

### 5. 具備狀態的對話代理人

雖然全面的狀態管理架構(state management architectures)採用比單純序列連結更複雜的方法，提示鏈結(Prompt chaining)提供了一個維持對話連貫性的基礎機制。此技術透過把每次對話回合建構為新提示(prompt)，系統性地納入先前互動提取的資訊或實體(entities)，從而維持脈絡。

* 提示(Prompt)1：處理使用者第一句話，識別意圖(intent)與關鍵實體(key entities)。
* 處理(Processing)：以意圖(intent)與實體(entities)更新對話狀態(conversation state)。
* 提示(Prompt)2：根據當前狀態生成回應，及／或識別下一項所需資訊。
* 對後續回合重複此流程，每次新的使用者發話都啟動利用累積對話歷史(conversation history)（狀態(state)）的鏈。

此原則是發展對話代理人(conversational agents)的基礎，使其能在延伸、多輪對話(multi-turn dialogues)中維持脈絡與連貫性。透過保留對話歷史，系統能理解並恰當回應依賴先前資訊的使用者輸入。

### 6. 程式碼生成與精煉

產出可運行程式碼的過程通常是一個多階段程序(multi-stage process)，需要把問題分解為一連串逐步執行的離散邏輯操作(discrete logical operations)。

* 提示(Prompt)1：理解使用者對程式碼函式(code function)的需求，生成偽程式碼(pseudocode)或大綱(outline)。
* 提示(Prompt)2：根據大綱撰寫初版程式碼草稿(code draft)。
* 提示(Prompt)3：識別程式碼中的潛在錯誤或可改進之處（可使用靜態分析工具(static analysis tool)或另一個大型語言模型(Large Language Model, LLM)呼叫）。
* 提示(Prompt)4：根據識別出的問題重寫或精煉程式碼。
* 提示(Prompt)5：加入文件(documentation)或測試案例(test cases)。

在AI輔助軟件開發(AI-assisted software development)等應用中，提示鏈結(Prompt chaining)的效用來自其把複雜編碼任務拆解為一系列可管理子問題的能力。此模組化結構降低大型語言模型(Large Language Model, LLM)於每個步驟的操作複雜度。更重要的是，該方法允許在模型呼叫之間插入確定性邏輯(deterministic logic)，在工作流程中實施中介資料處理(intermediate data processing)、輸出驗證(output validation)與條件分支(conditional branching)。透過此方式，單一且多元的請求不再導致不可靠或不完整的結果，而是被轉換為由底層執行框架(execution framework)管理的結構化操作序列。

### 7. 多模態與多步推理

分析包含多種模態(modalities)的資料集(datasets)需要把問題拆解為較小的、基於提示(prompt-based)的任務。例如，解讀一張包含圖片、嵌入文字(embedded text)、標籤(labels)指向特定文字區段，以及表格資料(tabular data)說明每個標籤的影像，就需要此種方法。

* 提示(Prompt)1：從使用者的影像請求中提取並理解文字。
* 提示(Prompt)2：把提取的影像文字與相應標籤連結。
* 提示(Prompt)3：利用表格(table)解讀已收集資訊，以決定所需輸出。

# 實作範例

實現提示鏈結(Prompt chaining)的方式可從腳本(script)中的直接順序函式呼叫延伸至管理控制流程(control flow)、狀態(state)與元件整合(component integration)的專門框架(frameworks)。LangChain(LangChain)、LangGraph(LangGraph)、Crew AI(Crew AI)及Google代理開發工具套件(Google Agent Development Kit, ADK)等框架提供結構化環境，用於建構與執行這些多步流程，特別有利於複雜架構。

為示範起見，LangChain(LangChain)與LangGraph(LangGraph)是合適的選擇，因為它們的核心應用程式介面(Application Programming Interfaces, APIs)專為組合鏈(chains)與圖(graphs)的操作而設計。LangChain(LangChain)提供線性序列(linear sequences)的基礎抽象(abstractions)，而LangGraph(LangGraph)擴展這些能力以支援有狀態(stateful)與循環(cyclical)計算，這些特性對實作更高階的代理行為(agentic behaviors)至關重要。本示例將聚焦於基本的線性序列(linear sequence)。

以下程式碼實作了一個兩步驟提示鏈(two-step prompt chain)，作為資料處理管線(data processing pipeline)。第一階段旨在解析未結構化文字(unstructured text)並提取特定資訊。下一階段接收提取的輸出並把它轉換成結構化資料格式(structured data format)。

要複製此程序，必須先安裝所需函式庫(libraries)。可透過以下命令完成：

```bash
pip install langchain langchain-community langchain-openai langgraph
```

請注意，langchain-openai可以依需要換成適用於其他模型提供者(model provider)的套件。接下來，必須為所選語言模型提供者，例如OpenAI、Google Gemini或Anthropic，設定必要的應用程式介面金鑰(API credentials)，以配置執行環境(execution environment)。

```python
import os
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

# For better security, load environment variables from a .env file
# from dotenv import load_dotenv
# load_dotenv()
# Make sure your OPENAI_API_KEY is set in the .env file

# Initialize the Language Model (using ChatOpenAI is recommended)

llm = ChatOpenAI(temperature=0)

# --- Prompt 1: Extract Information ---

prompt_extract = ChatPromptTemplate.from_template(
    "Extract the technical specifications from the following text:\n\n{text_input}"
)

# --- Prompt 2: Transform to JSON ---

prompt_transform = ChatPromptTemplate.from_template(
    "Transform the following specifications into a JSON object with 'cpu', 'memory', and 'storage' as keys:\n\n{specifications}"

)

# --- Build the Chain using LCEL ---
# The StrOutputParser() converts the LLM's message output to a simple string.
extraction_chain = prompt_extract | llm | StrOutputParser()

# The full chain passes the output of the extraction chain into the 'specifications'
# variable for the transformation prompt.
full_chain = (
    {"specifications": extraction_chain}
        |
    prompt_transform
        |
    llm
        |
    StrOutputParser()
)

# --- Run the Chain ---

input_text = "The new laptop model features a 3.5 GHz octa-core processor, 16GB of RAM, and a 1TB NVMe SSD."

# Execute the chain with the input text dictionary.
final_result = full_chain.invoke({"text_input": input_text})
print("\n--- Final JSON Output ---")
print(final_result)
```

這段Python程式碼展示如何使用LangChain(LangChain)函式庫處理文字。它運用了兩個獨立的提示(prompts)：第一個用於從輸入字串中提取技術規格(technical specifications)，第二個把這些規格格式化為JSON物件(JSON object)。ChatOpenAI(ChatOpenAI)模型負責語言模型互動，而StrOutputParser(StrOutputParser)確保輸出為可用字串。LangChain表達式語言(LangChain Expression Language, LCEL)優雅地把這些提示(prompts)與語言模型連接在一起。第一條鏈`extraction_chain`提取規格，`full_chain`則把提取出的輸出作為轉換提示(prompt)的輸入。範例輸入文字描述了一部手提電腦，`full_chain`以此文字啟動，經兩個步驟處理後，最終輸出為包含提取與格式化規格的JSON字串(JSON string)。

## 脈絡工程(Context Engineering)與提示工程(Prompt Engineering)

脈絡工程(Context Engineering)（見圖1）是指在語彙生成(token generation)之前，為AI模型設計、建構與提供完整資訊環境(informational environment)的系統化學科(discipline)。此方法認為，模型輸出的品質較少取決於模型架構(architecture)，而更依賴提供的脈絡(context)是否充足。

![Context Engineering](../assets/Context_Engineering.png)

圖1：脈絡工程(Context Engineering)是一門為AI建構豐富且全面資訊環境的學科，該脈絡的品質是成就進階代理式(agentic)效能的主要因素。

這種方法標誌著從傳統提示工程(Prompt Engineering)的重要進化，後者主要聚焦於優化使用者即時查詢(prompt)的措辭。脈絡工程(Context Engineering)擴展此範疇，涵蓋多層資訊，例如系統提示(System prompt)，即一組基礎指示，界定AI的操作參數，例如「你是一名技術寫作者(technical writer)；你的語氣必須正式且精準」。脈絡還會加入外部資料，包括檢索文件(retrieved documents)，AI主動從知識庫(knowledge base)抓取資訊以作回應，例如提取專案的技術規格。它亦包含工具輸出(tool outputs)，即AI使用外部應用程式介面(API)取得即時資料，例如查詢行事曆(calendar)以確定使用者的空檔。這些明確資料顯性地結合關鍵隱性資料，例如使用者身分(user identity)、互動歷史(interaction history)及環境狀態(environmental state)。核心原則是，即使先進模型如缺乏充分或精心構建的操作環境視角也難以發揮效能。

因此，這種實務把任務重新定位為不只是回答問題，而是為代理人(agent)建構全面的操作圖景(operational picture)。舉例說明，經脈絡工程(Context Engineering)的代理人(agent)不單回應查詢，還會先整合使用者的行事曆空檔（工具輸出(tool output)）、電郵收件人的專業關係（隱性資料(implicit data)）以及先前會議紀錄（檢索文件(retrieved documents)）。如此便能生成高度相關、個人化且實用的輸出。所謂「工程」(engineering)的部分，涉及建立強健的管線(pipelines)，在執行時擷取與轉換這些資料，並建立回饋循環(feedback loops)以持續提升脈絡品質。

要實踐此方法，可使用專門的調整系統(tuning systems)在大規模下自動改進流程。例如，Google的Vertex AI提示優化器(Vertex AI prompt optimizer)能透過根據一組樣本輸入(sample inputs)與預設評估指標(evaluation metrics)系統性評估回應，提升模型表現。此方法可在不同模型之間調整提示(prompts)與系統指示(system instructions)，而無需大量手動改寫。只要向該優化器提供樣本提示(sample prompts)、系統指示(system instructions)與模板(template)，它便能以程式化方式精煉脈絡輸入(contextual inputs)，提供實施脈絡工程(Context Engineering)所需回饋循環(feedback loops)的結構化方法。

這種結構化方法是區分初階AI工具與更先進、具脈絡感知(contextually-aware)系統的關鍵。它把脈絡(context)本身視為主要組件，重視代理人(agent)知道甚麼、何時知道以及如何運用該資訊。此實務確保模型對使用者意圖(intent)、歷史(history)與當前環境(current environment)有全面理解。最終，脈絡工程(Context Engineering)是推動無狀態聊天機械人(stateless chatbots)邁向高度能力、情境感知(situationally-aware)系統的關鍵方法論(methodology)。

## 重點一覽(At a Glance)

**內容：** 當複雜任務由單一提示(prompt)處理時，常令大型語言模型(Large Language Models, LLMs)不堪負荷，引致重大效能問題。模型的認知負荷(cognitive load)增加，錯誤機率亦隨之上升，例如忽視指示、失去脈絡以及生成錯誤資訊。單一巨型提示(monolithic prompt)難以有效管理多重約束與連續推理步驟，最終導致不可靠、不精準的輸出，因為大型語言模型(Large Language Model, LLM)無法處理請求的所有面向。

**原因：** 提示鏈結(Prompt chaining)透過把複雜問題拆解為一系列較小、互相關聯的子任務(sub-tasks)，提供標準化解決方案。鏈中每一步都使用聚焦的提示(prompt)執行特定操作，顯著提升可靠性與控制力。某個提示(prompt)的輸出會作為下一個提示(prompt)的輸入，建立一個邏輯工作流程(logical workflow)，逐步構建最終解決方案。這種模組化、分而治之(divide-and-conquer)的策略使流程更易管理、便於除錯，並允許在步驟之間整合外部工具或結構化資料格式(structured data formats)。此模式(pattern)是開發能夠規劃、推理與執行複雜工作流程的進階代理式(agentic)系統的基礎。

**經驗法則：** 當任務過於複雜而無法透過單一提示(prompt)完成、涉及多個獨立處理階段、需要在步驟之間與外部工具互動，或在建構需要多步推理(multi-step reasoning)並維持狀態(state)的代理式(agentic)系統時，應採用此模式(pattern)。

**視覺摘要：**

![Prompt Chaining Pattern](../assets/Prompt_Chaining_Pattern.png)

圖2：提示鏈結(Prompt chaining)模式(pattern)：代理人(agents)依序接收使用者的提示(prompts)，每個代理人的輸出皆作為下一位代理人的輸入。

## 重要啟示

以下為若干重要啟示：

* 提示鏈結(Prompt chaining)把複雜任務拆解為一連串較小、聚焦的步驟。有時亦稱為管道模式(Pipeline pattern)。
* 鏈中的每一步都涉及大型語言模型(Large Language Model, LLM)呼叫或處理邏輯(processing logic)，並把前一步的輸出作為輸入。
* 此模式(pattern)提升與語言模型(language models)進行複雜互動時的可靠性與可管理性。
* LangChain(LangChain)、LangGraph(LangGraph)與Google代理開發工具套件(Google Agent Development Kit, ADK)等框架提供強大工具，以定義、管理與執行這些多步序列(multi-step sequences)。

## 結論

透過把複雜問題拆解為一系列更簡單、易於管理的子任務，提示鏈結(Prompt chaining)為指導大型語言模型(Large Language Models, LLMs)提供堅實框架。這種「分而治之」(divide-and-conquer)策略讓模型一次專注於一個特定操作，大幅提升輸出的可靠性與控制力。作為基礎模式(pattern)，它促成能進行多步推理(multi-step reasoning)、整合工具(tool integration)與管理狀態(state management)的高階AI代理人(AI agents)。最終，掌握提示鏈結(Prompt chaining)對於建構穩健、具脈絡感知(context-aware)的系統至關重要，使其能執行遠超單一提示(prompt)能力的精細工作流程(workflows)。

## References

1. LangChain Documentation on LCEL: [https://python.langchain.com/v0.2/docs/core_modules/expression_language/](https://python.langchain.com/v0.2/docs/core_modules/expression_language/)
2. LangGraph Documentation: [https://langchain-ai.github.io/langgraph/](https://langchain-ai.github.io/langgraph/)
3. Prompt Engineering Guide - Chaining Prompts: [https://www.promptingguide.ai/techniques/chaining](https://www.promptingguide.ai/techniques/chaining)
4. OpenAI API Documentation (General Prompting Concepts): [https://platform.openai.com/docs/guides/gpt/prompting](https://platform.openai.com/docs/guides/gpt/prompting)
5. Crew AI Documentation (Tasks and Processes): [https://docs.crewai.com/](https://docs.crewai.com/)
6. Google AI for Developers (Prompting Guides): [https://cloud.google.com/discover/what-is-prompt-engineering?hl=en](https://cloud.google.com/discover/what-is-prompt-engineering?hl=en)
7. Vertex Prompt Optimizer [https://cloud.google.com/vertex-ai/generative-ai/docs/learn/prompts/prompt-optimizer](https://cloud.google.com/vertex-ai/generative-ai/docs/learn/prompts/prompt-optimizer)
