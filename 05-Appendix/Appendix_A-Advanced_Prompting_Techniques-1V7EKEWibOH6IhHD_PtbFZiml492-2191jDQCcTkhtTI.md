# 附錄 A：進階提示技巧 (Advanced Prompting Techniques)

## 提示入門 (Introduction to Prompting)

提示 (Prompting) 是與語言模型 (language models) 互動的主要介面，指的是設計輸入以引導模型產生期望輸出的過程。這涉及結構化請求、提供相關脈絡、指定輸出格式，以及示範期望的回應類型。設計良好的提示可以最大化語言模型的潛能，產生準確、相關且具創意的回應；相反地，設計不佳的提示則可能帶來模糊、不相關或錯誤的輸出。

提示工程 (prompt engineering) 的目標，是穩定地從語言模型取得高品質回應。這需要理解模型的能力與限制，並有效傳達目標，透過學習如何最佳地指示人工智能 (AI) 來培養溝通專業。

本附錄介紹超越基本互動方法的各種提示技巧，探討結構複雜請求、提升模型推理能力、控制輸出格式，以及整合外部資訊的方法。這些技巧可應用於從簡單聊天機器人到複雜多代理系統 (multi-agent systems) 的各種應用，並能改善代理式應用 (agentic applications) 的效能與可靠性。

代理式模式 (agentic patterns) 是建構智慧系統的架構骨幹，在主體章節已有詳細說明。這些模式定義代理 (agents) 如何規劃、運用工具、管理記憶與協作。代理式系統的效能取決於其與語言模型進行有意義互動的能力。

## 核心提示原則 (Core Prompting Principles)

有效提示的核心原則如下：

有效提示建立在與語言模型溝通的基礎原則上，適用於不同模型與任務複雜度。掌握這些原則是穩定產生有用且準確回應的關鍵。

**清晰與具體 (Clarity and Specificity)**：指令應當明確且精準。語言模型依據模式解讀，多重詮釋可能導致非預期回應。請定義任務、期望的輸出格式，以及任何限制或要求，避免模糊用語或假設。資訊不足的提示會造成模稜兩可與不準確的回應，阻礙有意義的輸出。

**簡潔 (Conciseness)**：在保持具體的同時，也不應犧牲簡潔。指令應直接，不必要的字句或複雜句式會讓模型困惑或掩蓋主要指令。提示應保持簡單；對使用者造成困惑的內容，多半也會讓模型困惑。避免繁複語句與多餘資訊，使用直接措辭與主動動詞，清楚界定期望行動。有效動詞包括：Act、Analyze、Categorize、Classify、Contrast、Compare、Create、Describe、Define、Evaluate、Extract、Find、Generate、Identify、List、Measure、Organize、Parse、Pick、Predict、Provide、Rank、Recommend、Return、Retrieve、Rewrite、Select、Show、Sort、Summarize、Translate、Write。

**運用動詞 (Using Verbs)**：動詞選擇是提示的核心工具。動作動詞指出預期操作。比起「想一想如何摘要這段文字」，直接指示「請摘要以下文字」更有效。精準動詞可引導模型啟動與該任務相關的訓練資料與流程。

**指令優於限制 (Instructions Over Constraints)**：正向指令通常比負向限制更有效。比起強調不該做什麼，直接說明期望行動更佳。限制仍可用於安全或嚴格格式需求，但過度依賴會讓模型聚焦於避免，而非達成目標。以指令直接引導模型，符合人類指導偏好並減少混淆。

**實驗與迭代 (Experimentation and Iteration)**：提示工程是反覆試驗的過程，找到最佳提示往往需要多次嘗試。先從草稿開始，測試、分析輸出、找出不足並修正提示。模型版本、設定（如 temperature 或 top-p），以及細微的措辭改動都可能產生不同結果。記錄嘗試歷程對學習與改進至關重要。實驗與迭代是達成目標效能的必要手段。

這些原則構成與語言模型有效溝通的基礎。優先追求清晰、簡潔、行動動詞、正向指令與反覆改進，可建立套用進階提示技巧的穩健框架。

## 基礎提示技巧 (Basic Prompting Techniques)

在核心原則之上，基礎技巧透過提供不同程度的資訊或範例，指引語言模型產生回應。這些方法是提示工程的起點，適用於廣泛的應用情境。

### 零範例提示 (Zero-Shot Prompting)

零範例提示 (Zero-shot prompting) 是最基本的提示形式，只提供指令與輸入資料，沒有展示期望的輸入輸出範例。它完全依賴模型的預訓練知識來理解任務並產生相關回應。本質上，零範例提示只包含任務描述與啟動過程的初始文字。

* **適用時機 (When to use)**：零範例提示常足以應付模型在訓練期間大量接觸過的任務，例如簡單問答、文字補完，或摘要結構明確的文本。這是最快速、最先嘗試的方法。
* **範例 (Example)**：
  請將以下英文句子翻譯成法文：'Hello, how are you?'

### 單範例提示 (One-Shot Prompting)

單範例提示 (One-shot prompting) 會在正式任務之前，提供一組輸入與對應期望輸出的範例。這種方法作為初步示範，說明模型應該複製的模式，讓模型可以參照具體實例完成任務。

* **適用時機 (When to use)**：當期望的輸出格式或風格較特殊或少見時，單範例提示很有用，因為它給模型具體範例可學習。若任務需要特定結構或語氣，單範例提示常比零範例提示更有效。
* **範例 (Example)**：
  請將以下英文句子翻譯成西班牙文：
  English: 'Thank you.'
  Spanish: 'Gracias.'

  English: 'Please.'
  Spanish:

### 少範例提示 (Few-Shot Prompting)

少範例提示 (Few-shot prompting) 是在單範例提示的基礎上提供多個輸入輸出範例，通常三到五組，以呈現更明確的期望模式，提高模型複製該模式的可能性。此方法透過多個範例，引導模型遵循特定輸出結構。

* **適用時機 (When to use)**：少範例提示特別適合需要遵守特定格式、風格或表現細微差異的任務。對於分類、依特定結構抽取資料，或生成具體風格文本等任務，若零範例或單範例效果不穩定，提供三到五個範例通常有幫助，可依任務複雜度與模型 token 限制調整。
* **範例品質與多樣性的重要性 (Importance of Example Quality and Diversity)**：少範例提示的效果高度依賴範例的品質與多樣性。範例應準確、代表任務，並涵蓋可能的變化與邊界情境。高品質、撰寫良好的範例至關重要，即使小錯誤也可能讓模型困惑並產生不良輸出。納入多樣化範例有助於模型在面對未知輸入時更好地泛化。
* **分類範例中混合類別 (Mixing Up Classes in Classification Examples)**：在分類任務使用少範例提示時，最好打亂不同類別範例的順序，避免模型過度擬合特定範例排列，確保它能獨立識別每個類別的關鍵特徵，提高在新資料上的穩健度。
* **演進至多範例學習 (Evolution to "Many-Shot" Learning)**：隨著現代大型語言模型 (LLMs) 例如 Gemini 在長脈絡建模上愈趨強大，它們能有效運用「多範例 (many-shot)」學習。這代表對於複雜任務，只要在提示中放入大量範例——有時甚至數百個——就能達到最佳效能，使模型學習更精細的模式。
* **範例 (Example)**：
  請將以下電影評論的情感分類為 POSITIVE、NEUTRAL 或 NEGATIVE：

  Review: "The acting was superb and the story was engaging."

## 架構化提示 (Structured Prompting)

超越提供範例的基本技巧，提示的結構方式在引導語言模型時扮演關鍵角色。結構化提示指的是在提示中使用不同段落或元素，以清楚有序地提供指令、脈絡或範例等不同類型資訊，幫助模型正確解析提示並理解每段文字的功能。

## 情境工程 (Contextual Engineering)

情境工程 (Context engineering) 不同於靜態系統提示 (system prompts)，它會動態提供任務與對話所需的背景資訊。這些不斷變化的資訊能協助模型掌握細微差異、記住過往互動並整合相關細節，產生更紮實的回應與流暢的互動。例子包括先前對話、相關文件（如檢索增強生成 (Retrieval Augmented Generation, RAG)）、或特定操作參數。例如在討論日本行程時，可請模型根據現有對話脈絡，推薦三個適合家庭的東京活動。在代理式系統中，情境工程是記憶持續、決策與跨子任務協調等核心行為的基礎。具備動態情境管線的代理能長期維持目標、調整策略並與其他代理或工具順暢協作——這些都是長期自主性的關鍵。

此方法論主張，模型輸出的品質更仰賴所提供情境的豐富程度，而非模型架構本身。它象徵傳統提示工程的重大演進：過去主要專注於優化即時使用者提問的措辭，如今則擴展至多層資訊。

這些層級包括：

* **系統提示 (System prompts)**：定義 AI 運作參數的基礎指令（例如「你是一名技術寫作人，語氣須正式且精確」）。
* **外部資料 (External data)**：
  * **檢索文件 (Retrieved documents)**：從知識庫主動抓取的資訊，以支援回應（例如擷取技術規格）。
  * **工具輸出 (Tool outputs)**：AI 使用外部 API 取得即時資料的結果（例如查詢行事曆以確認空檔）。

* **隱含資料 (Implicit data)**：如使用者身份、互動歷史與環境狀態等關鍵資訊。導入隱含情境涉及隱私與道德管理挑戰，因此在企業、醫療、金融等領域進行情境工程時，必須具備強而有力的治理機制。

核心原則是，即使是先進模型，在缺乏完整或結構良好的操作環境視角時，也會表現不佳。此實務將任務重新框定為為代理建立全面的操作圖像。例如，情境工程代理會在回應查詢前，整合使用者行事曆空檔（工具輸出）、與收件人的專業關係（隱含資料），以及前次會議紀錄（檢索文件）。如此一來，模型能產出高度相關、個人化且實用的回應。「工程」層面在於建立能在執行階段抓取並轉換資料的穩健管線，並設計回饋循環持續改善情境品質。

為達成此目標，可運用專用調校系統，例如 Google 的 Vertex AI 提示最佳化工具，自動在大規模情境下改進流程。透過系統化地以範例輸入與預先定義的指標評估回應，這些工具能提升模型表現，並在不同模型間調整提示與系統指令，而無需大量手動改寫。提供最佳化器範例提示、系統指令與範本，就能以程式化方式改進情境輸入，為高階情境工程所需的回饋循環提供結構化方法。

這種結構化方法讓基本 AI 工具與更精密、情境感知的系統區隔開來。它將情境視為主要組件，強調代理知道什麼、何時知道、以及如何運用資訊。此實務確保模型能全面理解使用者意圖、歷史與當下環境。最終，情境工程是將無狀態聊天機器人轉化為高度能幹、具情境意識系統的重要方法論。

## 結構化輸出 (Structured Output)

提示的目標往往不只是取得自由格式的文字回應，而是以特定且可被機器讀取的格式提取或生成資訊。要求結構化輸出，例如 JSON、XML、CSV 或 Markdown 表格，是重要的結構技巧。透過明確要求特定格式的輸出，並視需要提供結構範本或範例，可引導模型整理回應，方便代理式系統或應用的其他部分解析與使用。要求模型輸出 JSON 物件對資料抽取尤其有利，因為這會迫使模型建立結構，並減少幻覺。建議嘗試不同輸出格式，尤其在資料抽取或分類等非創意任務上。

* **範例 (Example)**：
  從以下文字中擷取資訊，並以 JSON 物件回傳，鍵為 `name`、`address` 與 `phone.number`。

  Text: "Contact John Smith at 123 Main St, Anytown, CA or call (555) 123-4567."

有效運用系統提示、角色設定、情境資訊、分隔符與結構化輸出，可大幅提升與語言模型互動的清晰度、控制力與實用性，為打造可靠的代理式系統奠定堅實基礎。請求結構化輸出是建立流程的關鍵，使語言模型的輸出能作為後續系統或處理步驟的輸入。

**運用 Pydantic 打造物件導向介面 (Leveraging Pydantic for an Object-Oriented Facade)**：強制結構化輸出並提升互通性的強大技巧，是使用 LLM 生成的資料填入 Pydantic 物件。Pydantic 是一套 Python 資料驗證與設定管理函式庫，運用 Python 型別註解 (type annotations)。透過定義 Pydantic 模型，可為期望的資料結構建立清楚且可強制的綱要。此作法為提示輸出提供物件導向外觀，將原始文字或半結構化資料轉換為經過驗證且具型別提示的 Python 物件。

你可以使用 `model_validate_json` 方法，將 LLM 產生的 JSON 字串直接解析為 Pydantic 物件。此方法特別實用，因為它在單一步驟中完成解析與驗證。

```python
from pydantic import BaseModel, EmailStr, Field, ValidationError
from typing import List, Optional
from datetime import date


# --- Pydantic Model Definition (from above) ---
class User(BaseModel):
    name: str = Field(..., description="The full name of the user.")
    email: EmailStr = Field(..., description="The user's email address.")
    date_of_birth: Optional[date] = Field(None, description="The user's date of birth.")
    interests: List[str] = Field(default_factory=list, description="A list of the user's interests.")


# --- Hypothetical LLM Output ---
llm_output_json = """
{
    "name": "Alice Wonderland",
    "email": "alice.w@example.com",
    "date_of_birth": "1995-07-21",
    "interests": [
        "Natural Language Processing",
        "Python Programming",
        "Gardening"
    ]
}
"""


# --- Parsing and Validation ---
try:
    # Use the model_validate_json class method to parse the JSON string.
    # This single step parses the JSON and validates the data against the User model.
    user_object = User.model_validate_json(llm_output_json)

    # Now you can work with a clean, type-safe Python object.
    print("Successfully created User object!")
    print(f"Name: {user_object.name}")
    print(f"Email: {user_object.email}")
    print(f"Date of Birth: {user_object.date_of_birth}")
    print(f"First Interest: {user_object.interests[0]}")

    # You can access the data like any other Python object attribute.
    # Pydantic has already converted the 'date_of_birth' string to a datetime.date object.
    print(f"Type of date_of_birth: {type(user_object.date_of_birth)}")
except ValidationError as e:
    # If the JSON is malformed or the data doesn't match the model's types,
    # Pydantic will raise a ValidationError.
    print("Failed to validate JSON from LLM.")
    print(e)
```

這段 Python 程式碼示範如何使用 Pydantic 定義資料模型並驗證 JSON。它定義一個包含姓名、電郵、出生日期與興趣欄位的 User 模型，內含型別註解與描述。程式接著透過 User 模型的 `model_validate_json` 方法，解析假想的大型語言模型 (LLM) 輸出。此方法同時處理 JSON 解析與資料驗證，遵循模型的結構與型別。最後，程式讀取驗證後的 Python 物件資料，並針對 JSON 無效時的 ValidationError 提供錯誤處理。

若是處理 XML 資料，可使用 xmltodict 函式庫將 XML 轉換為字典，再交給 Pydantic 模型解析。透過在 Pydantic 模型中使用 Field 別名，可順暢地把冗長或包含大量屬性的 XML 結構映射到物件欄位。

此方法對確保以 LLM 為核心的元件能與大型系統其他部分互通非常重要。當 LLM 輸出封裝在 Pydantic 物件內，就能自信地傳遞給其他函式、API 或資料處理流程，確保資料符合預期的結構與型別。在系統組件邊界採用「解析，不驗證 (parse, don't validate)」的做法，可打造更穩健且易於維護的應用。

有效運用系統提示、角色設定、情境資訊、分隔符與結構化輸出，可大幅提升與語言模型互動的清晰度、控制力與實用性，為打造可靠的代理式系統奠定堅實基礎。請求結構化輸出是建立流程的關鍵，使語言模型的輸出能作為後續系統或處理步驟的輸入。

## 推理與思考過程技巧 (Reasoning and Thought Process Techniques)

大型語言模型擅長模式辨識與文字生成，但在需要複雜多步推理的任務上仍常遇到挑戰。本附錄聚焦在鼓勵模型揭露內部思考流程的技巧，藉此增強推理能力，特別是改善邏輯推斷、數學計算與規劃。

## 思路鏈 (Chain of Thought, CoT)

思路鏈 (Chain of Thought, CoT) 提示是提升語言模型推理能力的強大方法，透過明確要求模型在給出最終答案前，生成中間推理步驟。與其只要求結果，不如指示模型「一步一步想」。這模仿人類將問題拆解成較小且可管理部分，並依序處理的方式。

CoT 有助於 LLM 產生更準確的答案，尤其在需要計算或邏輯推論的任務上，模型若缺乏推理步驟容易犯錯。藉由生成中介步驟，模型較能保持在正確軌道上，正確執行必要操作。

CoT 主要有兩種變化：

* **零範例 CoT (Zero-Shot CoT)**：只需在提示中加上「Let's think step by step」或類似語句，無須提供推理範例。令人驚訝的是，對許多任務而言，這樣簡單的改動就能顯著提升表現，因為它觸發模型展現內在推理軌跡的能力。
  * **範例 (Example, Zero-Shot CoT)**：
    If a train travels at 60 miles per hour and covers a distance of 240 miles, how long did the journey take? Let's think step by step.

* **少範例 CoT (Few-Shot CoT)**：結合少範例提示與 CoT，提供多個範例，同時展示輸入、逐步推理與最終輸出。這樣能讓模型更清楚地理解如何進行推理並組織回應，相較零範例 CoT，對複雜任務通常效果更佳。

  * **範例 (Example, Few-Shot CoT)**：
    Q: The sum of three consecutive integers is 36. What are the integers?
    A: Let the first integer be x. The next consecutive integer is x+1, and the third is x+2. The sum is x + (x+1) + (x+2) \= 3x + 3. We know the sum is 36, so 3x + 3 \= 36. Subtract 3 from both sides: 3x \= 33. Divide by 3: x \= 11. The integers are 11, 11+1=12, and 11+2=13. The integers are 11, 12, and 13.

    Q: Sarah has 5 apples, and she buys 8 more. She eats 3 apples. How many apples does she have left? Let's think step by step.

    A: Let's think step by step. Sarah starts with 5 apples. She buys 8 more, so she adds 8 to her initial amount: 5 + 8 \= 13 apples. Then, she eats 3 apples, so we subtract 3 from the total: 13 - 3 \= 10. Sarah has 10 apples left. The answer is 10.

CoT 的優勢很多：實作成本低，無需微調即可在現成 LLM 上發揮效用。更重要的是，模型輸出更具可解釋性，你可以看到它的推理步驟，有助於理解答案來源與除錯。此外，CoT 似乎能提升提示在不同語言模型版本間的穩健性，模型更新時性能較不易下滑。主要缺點是生成推理步驟會拉長輸出，增加 token 使用量，導致成本與回應時間提升。

運用 CoT 的最佳實務包括在推理步驟後呈現最終答案，因為推理過程會影響後續答案的 token 預測。另在單一正確答案（例如數學題）的任務中，建議將模型 temperature 設為 0（貪婪解碼），確保每步都選擇最可能的下一個 token。

## 自我一致性 (Self-Consistency)

自我一致性 (Self-Consistency) 建立在思路鏈概念之上，利用語言模型的機率特性提升推理可靠度。自我一致性不是依賴單一路徑，而是為同一問題生成多條多樣化推理路徑，再從中挑選最一致的答案。

自我一致性包含三個主要步驟：

1. **生成多條推理路徑 (Generate Diverse Reasoning Paths)**：以較高 temperature 執行模型多次，讓它針對同一問題產生不同的思考方式與推論步驟。
2. **擷取答案 (Extract the Answer)**：從每條推理路徑中擷取最終答案。
3. **選擇最常見答案 (Choose the Most Common Answer)**：對擷取的答案進行多數決，最常出現的答案被視為最一致、最有可能正確的結果。

此方法能提升回應的準確性與一致性，尤其適用於存在多種有效推理路徑，或模型單次嘗試易出錯的任務。其好處是提供答案正確性的類機率評估，整體提高準確率。然而，缺點是同一問題需多次執行模型，計算量與成本大幅增加。

* **概念範例 (Example, Conceptual)**：
  * *Prompt:* "Is the statement 'All birds can fly' true or false? Explain your reasoning."
  * *Model Run 1 (High Temp):* Reasons about most birds flying, concludes True.
  * *Model Run 2 (High Temp):* Reasons about penguins and ostriches, concludes False.
  * *Model Run 3 (High Temp):* Reasons about birds *in general*, mentions exceptions briefly, concludes True.
  * *Self-Consistency Result:* Based on majority vote (True appears twice), the final answer is "True". (Note: A more sophisticated approach would weigh the reasoning quality).

## 退一步思考提示 (Step-Back Prompting)

退一步思考提示 (Step-back prompting) 透過先要求語言模型思考與任務相關的一般原則或概念，再回到具體細節來增進推理。模型對較廣泛問題的回應會被用作解題脈絡。

此流程可讓語言模型喚起相關背景知識與更廣泛的推理策略。先聚焦基礎原則或較高層的抽象概念，模型更能生成準確且具洞見的答案，較不受表面細節影響。先考慮一般因素也能為後續生成特定創意輸出奠定更穩固基礎。退一步思考提示鼓勵批判性思維與知識應用，並可能透過強調通則來減少偏誤。

* **範例 (Example)**：
  * *Prompt 1 (Step-Back):* "What are the key factors that make a good detective story?"
  * *Model Response 1:* (Lists elements like red herrings, compelling motive, flawed protagonist, logical clues, satisfying resolution).
  * *Prompt 2 (Original Task + Step-Back Context):* "Using the key factors of a good detective story [insert Model Response 1 here], write a short plot summary for a new mystery novel set in a small town."

## 思想之樹 (Tree of Thoughts, ToT)

思想之樹 (Tree of Thoughts, ToT) 是擴充思路鏈的進階推理技巧，允許語言模型同時探索多條推理路徑，而非僅沿單一路徑前進。此技巧採用樹狀結構，每個節點代表一個「想法 (thought)」，即具體的語句序列作為中間步驟。從每個節點，模型可向外延伸，探索替代推理路徑。

ToT 特別適合需要探索、回溯或評估多種可能性才能找到解方的複雜問題。雖然比線性的思路鏈在計算上更昂貴、實作更複雜，但在需要深思熟慮與探索性問題解決的任務上，ToT 能達到更佳成效。它讓代理能考慮不同觀點，並透過探索其他分支來修正初始錯誤。

* **概念範例 (Example, Conceptual)**：針對複雜的創意寫作任務，例如「根據以下劇情重點，為故事設計三個不同結局」，ToT 讓模型從關鍵轉折點探索不同敘事分支，而非只生成單線延續。

這些推理與思考過程技巧對於打造能處理超越單純資訊擷取或文字生成的代理至關重要。透過提示模型展露推理、考慮多重觀點，或先回到一般原則，我們能大幅提升代理在代理式系統中執行複雜認知任務的能力。

## 行動與互動技巧 (Action and Interaction Techniques)

智慧代理不僅能生成文字，還能主動與環境互動，包括運用工具、執行外部函式，以及進行觀察—推理—行動的迭代循環。本節探討用於啟用這些主動行為的提示技巧。

## 工具使用／函式呼叫 (Tool Use / Function Calling)

代理的一項關鍵能力，是運用外部工具或呼叫函式，執行超出內部能力範圍的動作，包括網路搜尋、資料庫存取、寄送電子郵件、進行計算，或與外部 API 互動。為工具使用設計有效提示，需要指導模型何時以及如何恰當運用工具。

現代語言模型經常針對「函式呼叫 (function calling)」或「工具使用 (tool use)」進行微調，使其能解讀可用工具的說明，包括用途與參數。當收到使用者請求時，模型可判斷是否需要使用工具、識別合適的工具，並格式化所需參數以進行呼叫。模型本身不會直接執行工具，而是生成結構化輸出（通常為 JSON），指出工具與參數。代理式系統會處理這份輸出、執行工具，並將結果回傳給模型，整合至持續的互動中。

* **範例 (Example)**：
  你可使用一個天氣工具取得特定城市的即時天氣。工具名稱為 `get.current.weather`，需要 `city` 參數（字串）。

  User: What's the weather like in London right now?

  * *預期模型輸出 (Expected Model Output, Function Call):*
    {
      "tool.code": "get.current.weather",
      "tool.name": "get.current.weather",
      "parameters": {
        "city": "London"
      }
    }

## ReAct（推理與行動, Reason & Act）

ReAct（Reason & Act）是一種提示範式，將思路鏈式推理與使用工具行動的能力交錯結合。ReAct 模仿人類的運作方式——我們會用語言推理，並採取行動蒐集資訊或推進目標。

ReAct 模式包含以下循環：

1. **思考 (Thought)**：模型生成思考過程，說明目前理解與計劃。
2. **行動 (Action)**：根據思考決定採取的行動，通常是使用工具（如搜尋、計算機、API 呼叫），並輸出工具名稱與所需輸入。
3. **觀察 (Observation)**：代理式系統執行工具，並將結果（Observation）回饋給模型。
4. 模型根據最新觀察生成新的「思考」，進而導致更多「行動」與「觀察」，直到完成任務並輸出「最終答案 (Final Answer)」。

這種思考與行動交錯的流程，使代理能動態蒐集資訊、回應工具輸出並調整策略，對需與動態環境或外部知識來源互動的任務特別有效。

* **示意 ReAct 軌跡 (Example, Illustrative ReAct Trace)**：
  User: What is the capital of France and its current population?

  Thought: The user is asking for two pieces of information: the capital of France and its population. I can use a search tool to find this information. I will start by confirming the capital, although I am likely to know it.
  Action: Search({"query": "capital of France"})
  Observation: Paris

  Thought: Okay, the capital is Paris. Now I need to find the population of Paris.
  Action: Search({"query": "current population of Paris"})
  Observation: The population of Paris was estimated to be around 2.1 million people in 2023.

  Thought: I have found both pieces of information. I can now provide the final answer to the user.
  Final Answer: The capital of France is Paris, and its estimated population in 2023 was around 2.1 million people.

這些技巧對打造能主動與世界互動、擷取即時資訊並執行需與外部系統互動的任務之代理至關重要。

## 進階技巧 (Advanced Techniques)

在基礎、結構與推理模式之外，尚有多種提示技巧能進一步提升代理式系統的能力與效率，從運用 AI 最佳化提示，到整合外部知識與根據使用者特徵量身打造回應。

### 自動提示工程 (Automatic Prompt Engineering, APE)

由於撰寫有效提示是一個複雜且需反覆試驗的過程，自動提示工程 (Automatic Prompt Engineering, APE) 探索運用語言模型自動生成、評估與改進提示。此法旨在自動化提示撰寫流程，減少大量人工作業，並提升模型表現。

一般做法是使用「元模型 (meta-model)」或流程，將任務描述轉換為多個候選提示，再根據這些提示在一組輸入上的輸出品質評估表現（可使用 BLEU、ROUGE 等指標或人工評估）。選出表現最佳的提示，必要時進一步精修，並應用於目標任務。以 LLM 產生訓練聊天機器人的使用者提問變體即是一例。

* **概念範例 (Example, Conceptual)**：開發者提供描述：「我需要一個能從電子郵件中抽取日期與寄件人的提示。」APE 系統會生成多個候選提示，並在範例電郵上測試，選出能穩定抽取正確資訊的提示。

以下是使用 DSPy 等框架進行程式化提示最佳化的重述與延伸說明：

另一種強力的提示最佳化技巧（由 DSPy 框架大力推廣），是將提示視為可自動最佳化的程式化模組。此方法超越人工嘗試錯誤，以更系統化、資料驅動的方式進行。

此技巧的核心倚賴兩個關鍵組件：

1. **黃金資料集 (Goldset)**：具代表性的高品質輸入輸出配對集合，作為定義任務成功回應的「黃金標準」。
2. **目標函式 (Objective Function)**：自動評估 LLM 輸出與黃金資料對應輸出差異的函式，回傳評分以表示品質、準確度或正確性。

透過這兩個組件，最佳化器（例如貝葉斯最佳化器 (Bayesian optimizer)）會系統化地微調提示。此流程通常包含兩種策略，可單獨或結合使用：

* **少範例示例最佳化 (Few-Shot Example Optimization)**：最佳化器不再由開發者手動挑選範例，而是程式化地從黃金資料集中抽樣不同範例組合進行測試，找出最能引導模型產生期望輸出的範例集合。

* **指令提示最佳化 (Instructional Prompt Optimization)**：最佳化器自動微調提示核心指令，運用 LLM 作為「元模型」反覆改寫提示文字，調整措辭、語氣或結構，以找出能在目標函式下獲得最高分的表達方式。

這兩種策略的終極目標，都是最大化目標函式的評分，讓提示的輸出持續接近高品質黃金資料。結合兩種方法，系統可同時最佳化「給模型什麼指令」與「展示哪些範例」，打造針對特定任務、機器最佳化的強大提示。

### 迭代提示／精修 (Iterative Prompting / Refinement)

此技巧從簡單提示出發，根據模型初始回應逐步精修。若輸出不理想，就分析不足並修改提示加以改善。這比較像人工主導的反覆設計流程，而非 APE 那樣的自動化程序。

* **範例 (Example)**：
  * *Attempt 1:* "Write a product description for a new type of coffee maker."（結果過於泛泛）
  * *Attempt 2:* "Write a product description for a new type of coffee maker. Highlight its speed and ease of cleaning."（結果較好，但仍缺細節）
  * *Attempt 3:* "Write a product description for the 'SpeedClean Coffee Pro'. Emphasize its ability to brew a pot in under 2 minutes and its self-cleaning cycle. Target busy professionals."（結果更貼近需求）

### 提供負面範例 (Providing Negative Examples)

雖然「指令優於限制」的原則通常成立，但在特定情況下提供負面範例仍有幫助，需謹慎使用。負面範例展示輸入與不期望的輸出，或說明不應生成的回應，有助於釐清界線或避免特定錯誤。

* **範例 (Example)**：
  生成巴黎熱門旅遊景點清單，但不要包含艾菲爾鐵塔。

  不要這樣做的範例：
  Input: List popular landmarks in Paris.
  Output: The Eiffel Tower, The Louvre, Notre Dame Cathedral.

### 使用類比 (Using Analogies)

以類比框定任務，有時能讓模型將需求聯想到熟悉的概念，進而更好地理解期望輸出或流程。這對創意任務或說明複雜角色尤其有用。

* **範例 (Example)**：
  扮演「資料主廚」，將原始食材（資料點）烹調成突出主要風味（趨勢）的「摘要佳餚」，供商務讀者享用。

### 分工思維／拆解 (Factored Cognition / Decomposition)

針對非常複雜的任務，可將整體目標拆解成較小、易管理的子任務，並分別對每個子任務進行提示。最後再將子任務成果整合，達成最終結果。這與提示串接 (prompt chaining) 與規劃相關，但更強調刻意地拆解問題。

* **範例 (Example)**：撰寫研究論文：
  * Prompt 1: "Generate a detailed outline for a paper on the impact of AI on the job market."
  * Prompt 2: "Write the introduction section based on this outline: [insert outline intro]."
  * Prompt 3: "Write the section on 'Impact on White-Collar Jobs' based on this outline: [insert outline section]."（對其他章節重複）
  * Prompt N: "Combine these sections and write a conclusion."

### 檢索增強生成 (Retrieval Augmented Generation, RAG)

RAG 是強化語言模型的技巧，讓模型在提示過程中取得外部、最新或領域專屬資訊。當使用者提問時，系統會先從知識庫（例如資料庫、文件集或網路）檢索相關文件或資料，再將這些資訊作為脈絡加入提示，讓語言模型基於外部知識生成回應。這能減少幻覺，並提供模型未受訓練或最新的資訊。RAG 是需要處理動態或專有資訊的代理式系統關鍵模式。

* **範例 (Example)**：
  * *User Query:* "What are the new features in the latest version of the Python library 'X'?"
  * *System Action:* 搜尋文件資料庫中的 "Python library X latest features"。
  * *Prompt to LLM:* "Based on the following documentation snippets: [insert retrieved text], explain the new features in the latest version of Python library 'X'."

### 人物設定模式 (Persona Pattern, User Persona)

人物設定模式 (Persona pattern) 在提示中為模型定義特定的使用者角色或背景，使模型能根據該角色的需求、語氣或偏好量身定制回應。透過提供使用者的目標、經驗、痛點與語言風格等詳細描述，模型可以生成更符合情境的回答，提升體驗。這對客服、教育、健康照護等需要針對不同族群調整語氣與內容的應用特別有效。

### 程式碼提示 (Code Prompting)

語言模型——尤其訓練於大量程式碼資料的模型——是開發者的強大助手。程式碼提示 (Code prompting) 涵蓋請模型生成、解說、翻譯或除錯程式碼等多元用途：

* **撰寫程式碼的提示 (Prompts for writing code)**：根據需求描述請模型生成程式碼片段或函式。
  * **範例 (Example)**："Write a Python function that takes a list of numbers and returns the average."
* **解說程式碼的提示 (Prompts for explaining code)**：提供程式碼片段，請模型逐行或概述說明其功能。
  * **範例 (Example)**："Explain the following JavaScript code snippet: [insert code]."
* **翻譯程式碼的提示 (Prompts for translating code)**：請模型將程式碼從一種語言翻譯到另一種語言。
  * **範例 (Example)**："Translate the following Java code to C++: [insert code]."
* **除錯與審查程式碼的提示 (Prompts for debugging and reviewing code)**：提供有錯誤或可改進的程式碼，請模型找出問題、建議修復或提出重構建議。
  * **範例 (Example)**："The following Python code is giving a 'NameError'. What is wrong and how can I fix it? [insert code and traceback]."

有效的程式碼提示通常需要提供足夠脈絡，指定語言與版本，並明確說明功能或問題。

### 多模態提示 (Multimodal Prompting)

雖然本附錄與當前多數 LLM 互動以文字為主，但此領域正快速邁向能處理與生成多種型態（文字、影像、音訊、影片等）的多模態模型。多模態提示 (Multimodal prompting) 指使用複合輸入引導模型，而非僅使用文字。

* **範例 (Example)**：提供一張流程圖的圖片，請模型說明圖中流程（影像輸入＋文字提示）；或提供圖片請模型生成描述性標題（影像輸入＋文字提示 → 文字輸出）。

隨著多模態能力日益成熟，提示技巧也會演進，以善用這些結合輸入與輸出的方式。

## 最佳實務與實驗 (Best Practices and Experimentation)

成為熟練的提示工程師是持續學習與實驗的反覆過程，有幾項值得重申與強調的最佳實務：

* **提供範例 (Provide Examples)**：給予單範例或少範例是引導模型最有效的方法之一。
* **以簡馭繁 (Design with Simplicity)**：保持提示簡潔、清楚、易懂，避免多餘術語或過度複雜的措辭。
* **明確定義輸出 (Be Specific about the Output)**：清楚界定模型輸出的格式、長度、風格與內容。
* **指令優先於限制 (Use Instructions over Constraints)**：著重說明希望模型做什麼，而非不希望它做什麼。
* **控制最大 token 長度 (Control the Max Token Length)**：透過模型設定或提示指令管理輸出長度。
* **在提示中使用變數 (Use Variables in Prompts)**：在應用中使用提示時，可利用變數讓提示具有動態性與可重用性，避免硬編碼特定數值。
* **嘗試不同輸入格式與寫作風格 (Experiment with Input Formats and Writing Styles)**：嘗試不同的提示表述（提問、陳述、指令），並測試不同語氣與風格，尋找最佳結果。
* **在分類任務的少範例提示中打亂類別 (For Few-Shot Prompting with Classification Tasks, Mix Up the Classes)**：隨機排列不同類別的範例，避免過度擬合。
* **適應模型更新 (Adapt to Model Updates)**：語言模型持續更新，須準備在新版本上測試既有提示並調整，以善用新能力或維持效能。
* **嘗試輸出格式 (Experiment with Output Formats)**：尤其在非創意任務中，嘗試請求 JSON 或 XML 等結構化輸出。
* **與其他提示工程師共同實驗 (Experiment Together with Other Prompt Engineers)**：與他人合作可帶來不同觀點，協助找出更有效的提示。
* **思路鏈最佳實務 (CoT Best Practices)**：記得在推理後呈現答案，並在單一正解的任務中將 temperature 設為 0。
* **記錄各種提示嘗試 (Document the Various Prompt Attempts)**：追蹤哪些方式有效、哪些無效以及原因，維護結構化的提示、設定與結果紀錄。
* **在程式碼庫儲存提示 (Save Prompts in Codebases)**：在應用中整合提示時，將其存放於獨立且組織良好的檔案，方便維護與版本控制。
* **仰賴自動化測試與評估 (Rely on Automated Tests and Evaluation)**：在生產系統中，實作自動化測試與評估流程，以監控提示效能並確保能對新資料泛化。

提示工程是一項隨著實作而精進的技能。透過套用上述原則與技巧，並維持系統化的實驗與紀錄流程，你能大幅提升打造有效代理式系統的能力。

## 結論 (Conclusion)

本附錄全面檢視提示，將其重新定義為嚴謹的工程實務，而非單純提問。核心目的在於展示如何將通用語言模型轉化為針對特定任務的專業、可靠且高效工具。這段旅程從不可或缺的核心原則——清晰、簡潔與反覆實驗——開始，它們是與 AI 有效溝通的基石。這些原則之所以重要，在於它們能降低自然語言固有的模糊性，引導模型的機率性輸出朝單一正確意圖靠攏。在此基礎上，零範例、單範例與少範例提示等基本技巧，是透過範例展示期望行為的主要方法，提供不同程度的脈絡指引，強而有力地塑造模型的回應風格、語氣與格式。除此之外，結合明確角色、系統層級指令與清楚分隔的提示結構，提供精細控制模型的必要架構層。

在建構自主代理的脈絡下，這些技巧顯得格外重要，因為它們提供執行複雜多步操作所需的掌控力與可靠性。代理要有效擬定與執行計畫，必須運用思路鏈與思想之樹等進階推理模式，強迫模型外化邏輯步驟，將複雜目標拆解為可管理的子任務。整個代理式系統的運作可靠度，取決於各組件輸出的可預測性。因此，請求 JSON 等結構化資料並以 Pydantic 等工具進行程式化驗證，不只是方便，更是打造穩健自動化所必需。若欠缺這種嚴謹性，代理的內部認知組件便無法可靠溝通，最終在自動化流程中造成災難性失誤。總括而言，這些結構與推理技巧，正是將模型的機率性文字生成，轉化為代理可倚賴的、可預測的認知引擎的關鍵。

此外，這些提示賦予代理感知與行動環境的關鍵能力，彌合數位思考與現實互動的差距。像 ReAct 與原生函式呼叫等行動導向框架，是代理的「雙手」，讓它能使用工具、查詢 API、操作資料；同時，檢索增強生成與情境工程等技巧則扮演代理的「感官」，主動從外部知識庫擷取相關、即時資訊，確保代理的決策植根於當下的事實。這項能力避免代理孤立運作於靜態且可能過時的訓練資料中。掌握這整套提示光譜，是將通用語言模型從簡單文字生成器，提升為能自主、具覺察與智慧地執行複雜任務的真正代理之決定性技能。

## References

Here is a list of resources for further reading and deeper exploration of prompt engineering techniques:

1. Prompt Engineering, [https://www.kaggle.com/whitepaper-prompt-engineering](https://www.kaggle.com/whitepaper-prompt-engineering)
2. Chain-of-Thought Prompting Elicits Reasoning in Large Language Models, [https://arxiv.org/abs/2201.11903](https://arxiv.org/abs/2201.11903)
3. Self-Consistency Improves Chain of Thought Reasoning in Language Models,  [https://arxiv.org/pdf/2203.11171](https://arxiv.org/pdf/2203.11171)
4. ReAct: Synergizing Reasoning and Acting in Language Models, [https://arxiv.org/abs/2210.03629](https://arxiv.org/abs/2210.03629)
5. Tree of Thoughts: Deliberate Problem Solving with Large Language Models,  [https://arxiv.org/pdf/2305.10601](https://arxiv.org/pdf/2305.10601)
6. Take a Step Back: Evoking Reasoning via Abstraction in Large Language Models, [https://arxiv.org/abs/2310.06117](https://arxiv.org/abs/2310.06117)
7. DSPy: Programming—not prompting—Foundation Models [https://github.com/stanfordnlp/dspy](https://github.com/stanfordnlp/dspy)
