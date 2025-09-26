# 第四章：反思（Reflection）

## 反思（Reflection）模式概覽

在前幾章中，我們已探討多個基礎的代理（Agentic）模式：用於順序執行的鏈結（Chaining）、用於動態路徑選擇的導向（Routing），以及支援同時處理任務的並行化（Parallelization）。這些模式讓代理能更高效、靈活地處理複雜任務。然而，即使擁有精心設計的工作流程，代理最初的輸出或計劃也可能並非最佳、準確或完整。這正是**反思（Reflection）**模式發揮作用的地方。

反思（Reflection）模式涉及代理評估自己的工作、輸出或內在狀態，並利用該評估來提升表現或完善回應。這是一種自我修正或自我改進的形式，讓代理能在回饋、內部批判或與既定標準比較後，不斷改善輸出或調整策略。有時候，反思（Reflection）可以由一個專門負責分析初始代理輸出的獨立代理來協助。

與簡單的順序鏈結（Sequential Chain）直接把輸出傳遞到下一步，或單純選擇路徑的導向（Routing）不同，反思（Reflection）引入了回饋循環。代理不只是產出輸出，而是檢視該輸出（或生成輸出的過程）、找出可能的問題或改進空間，並利用這些洞察生成更好的版本或調整日後的行動。

這個流程通常包含以下步驟：

1. **執行：** 代理執行任務或產生初步輸出。
2. **評估／批判：** 代理（通常透過另一個大型語言模型（LLM）呼叫或一組規則）分析前一步的結果。這個評估可能檢查事實正確性、連貫性、風格、完整性、遵守指示等相關標準。
3. **反思／優化：** 根據批判，代理決定如何改進。這可能涉及生成改良版輸出、調整下一步的參數，甚至修改整體計劃。
4. **迭代（可選但常見）：** 經過優化的輸出或調整後的策略可以再次執行，反思（Reflection）流程可重複進行，直到達到滿意結果或符合停止條件。

一種關鍵且效果顯著的反思（Reflection）實作方式，是把流程分成兩個明確的邏輯角色：產出者（Producer）與批評者（Critic）。這通常被稱為「生成者－批評者（Generator-Critic）」或「產出者－審稿者（Producer-Reviewer）」模型。雖然單一代理可以進行自我反思（Self-Reflection），但使用兩個具備專長的代理（或兩個具有不同系統提示的大型語言模型（LLM）呼叫）往往能提供更穩健且更公正的成果。

1. 產出者代理（Producer Agent）：這個代理的主要責任是初步執行任務。它專注於生成內容，無論是撰寫程式碼、撰寫博客文章或建立計劃。它接收初始提示並產出第一版輸出。

2. 批評者代理（Critic Agent）：這個代理的唯一目的，是評估產出者生成的輸出。它會收到不同的一組指示，經常是不同的角色設定（例如「你是一名資深軟體工程師」、「你是一位一絲不苟的事實查核員」）。批評者的指示引導它根據具體標準——例如事實正確性、程式碼品質、風格要求或完整性——來分析產出者的作品。它的目標是找出缺陷、提出改進建議並提供結構化的回饋。

這種職責分離之所以強大，是因為它能避免代理審查自己作品時的「認知偏見」。批評者代理以全新視角檢視輸出，專注於找出錯誤和改進空間。批評者提供的回饋再傳回給產出者代理，作為生成新版本或經過優化輸出的指南。LangChain 和 ADK 的範例程式都實作了這種雙代理模型：LangChain 範例使用特定的 `reflector_prompt` 來建立批評者人格，而 ADK 範例則明確定義了產出者與審稿者代理。

實作反思（Reflection）通常需要調整代理工作流程，以納入這些回饋循環。這可以透過程式中的迭代迴圈實現，或使用支援狀態管理與根據評估結果進行條件轉換的框架。雖然單一步驟的評估與優化可以在 LangChain／LangGraph、ADK 或 Crew.AI 的鏈結中完成，但真正的迭代反思（Reflection）往往涉及更複雜的協調。

反思（Reflection）模式對於建立能產出高品質輸出、處理細緻任務並展現一定自我覺察與適應力的代理至關重要。它讓代理從僅僅執行指示，進階到更具深度的問題解決與內容生成模式。

值得注意的是，反思（Reflection）與目標設定及監控（Goal Setting and Monitoring，詳見第十一章）的交集。目標為代理的自我評估提供終極標準，而監控（Monitoring）則追蹤其進度。在多個實務案例中，反思（Reflection）可以充當修正引擎，利用監控得來的回饋來分析偏差並調整策略。這種協同作用將代理從被動執行者轉化為主動、有目的地朝既定目標適應性前進的系統。

此外，當大型語言模型（LLM）保留對話記憶（詳見第八章）時，反思（Reflection）模式的效能會顯著提升。這段對話歷史在評估階段提供關鍵脈絡，讓代理不僅評估單一輸出，也能參考先前互動、使用者回饋與演變中的目標。它使代理得以從過往批判中學習，避免重複犯錯。若沒有記憶，每次反思（Reflection）都是獨立事件；有了記憶，反思（Reflection）便成為累積過程，每一輪迭代都建立在上一輪的基礎上，導向更聰明且具脈絡意識的優化。

## 實務應用與使用案例

在輸出品質、準確性或遵守複雜限制至關重要的情境中，反思（Reflection）模式極具價值：

### 1. 創意寫作與內容生成

精煉生成的文字、故事、詩歌或行銷文案。

* **使用案例：** 代理撰寫博客文章。
  * **反思（Reflection）：** 生成草稿，針對流暢度、語氣和清晰度進行批判，然後依據批判重寫。重複此流程，直到文章符合品質標準。
  * **好處：** 產出更精煉且具效益的內容。

### 2. 程式碼生成與除錯

撰寫程式碼、找出錯誤並修正。

* **使用案例：** 代理撰寫 Python 函式。
  * **反思（Reflection）：** 撰寫初版程式碼，執行測試或靜態分析，找出錯誤或效率問題，然後根據發現進行修改。
  * **好處：** 生成更穩健且可運作的程式碼。

### 3. 複雜問題求解

評估多步推理任務中的中間步驟或建議方案。

* **使用案例：** 代理解決邏輯謎題。
  * **反思（Reflection）：** 提出一個步驟，評估其是否讓結果更接近解答或帶來矛盾，必要時回溯或選擇不同步驟。
  * **好處：** 提升代理在複雜問題空間中導航的能力。

### 4. 摘要與資訊綜合

精煉摘要以提升準確性、完整性與精簡程度。

* **使用案例：** 代理為長篇文件撰寫摘要。
  * **反思（Reflection）：** 生成初步摘要，與原始文件中的重點比較，調整摘要以納入遺漏資訊或提升準確度。
  * **好處：** 產出更準確且全面的摘要。

### 5. 規劃與策略

評估提議計劃並找出可能的缺陷或改進。

* **使用案例：** 代理規劃一系列達成目標的行動。
  * **反思（Reflection）：** 生成計劃，模擬執行或針對限制條件評估可行性，依據評估結果修訂計劃。
  * **好處：** 制定更有效且切實可行的計劃。

### 6. 會話式代理

檢視對話中先前的輪次，以維持脈絡、修正誤解或提升回應品質。

* **使用案例：** 客戶支援聊天機器人。
  * **反思（Reflection）：** 在使用者回應後，檢視對話歷史與上一則訊息，確保連貫性並準確回應使用者最新輸入。
  * **好處：** 促成更自然且有效的對話。

反思（Reflection）為代理系統增加一層後設認知，使它們能從自身輸出與流程中學習，產出更聰明、可靠且高品質的成果。

## 實作範例（LangChain）

完整、迭代的反思（Reflection）流程需要狀態管理與循環執行機制。雖然這些功能在像 LangGraph 等圖形化框架或客製程序碼中可原生處理，但反思（Reflection）循環的基本原理可以透過 LCEL（LangChain Expression Language）的組合語法有效示範。

以下範例使用 LangChain 函式庫與 OpenAI 的 GPT-4o 模型，實作反思（Reflection）迴圈來反覆生成並優化計算整數階乘的 Python 函式。流程從任務提示開始，產生初版程式碼，然後透過模擬資深軟體工程師角色的批判，逐步反思（Reflection）程式碼，每次迭代都加以優化，直到批判階段判定程式碼完美或達到最大迭代次數為止。最後會輸出經過優化的程式碼。

首先，請確保已安裝必要的函式庫：

```bash
pip install langchain langchain-community langchain-openai
```

你還需要在環境中設定所選語言模型的 API 金鑰（例如 OpenAI、Google Gemini、Anthropic）。

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.messages import SystemMessage, HumanMessage


# --- 設定 ---
# 從 .env 檔案載入環境變數（包含 OPENAI_API_KEY）
load_dotenv()

# 檢查 API 金鑰是否已設定
if not os.getenv("OPENAI_API_KEY"):
    raise ValueError("OPENAI_API_KEY not found in .env file. Please add it.")

# 初始化 Chat LLM。我們使用 gpt-4o 以獲得更佳推理能力。
# 使用較低的溫度以獲得更具決定性的輸出。
llm = ChatOpenAI(model="gpt-4o", temperature=0.1)


def run_reflection_loop():
    """
    示範多步驟的人工智慧反思（Reflection）迴圈，逐步改進一個 Python 函式。
    """
    # --- 核心任務 ---
    task_prompt = """
    Your task is to create a Python function named `calculate_factorial`.
    This function should do the following:
    1.  Accept a single integer `n` as input.
    2.  Calculate its factorial (n!).
    3.  Include a clear docstring explaining what the function does.
    4.  Handle edge cases: The factorial of 0 is 1.
    5.  Handle invalid input: Raise a ValueError if the input is a negative number.
    """

    # --- 反思（Reflection）迴圈 ---
    max_iterations = 3
    current_code = ""

    # 建立對話歷史以在每一步提供脈絡。
    message_history = [HumanMessage(content=task_prompt)]

    for i in range(max_iterations):
        print("\n" + "=" * 25 + f" REFLECTION LOOP: ITERATION {i + 1} " + "=" * 25)

        # --- 1. 生成／優化階段 ---
        # 第一次迭代用於生成，其後的迭代則根據批判進行優化。
        if i == 0:
            print("\n>>> STAGE 1: GENERATING initial code...")
            # 第一則訊息僅包含任務提示。
            response = llm.invoke(message_history)
            current_code = response.content
        else:
            print("\n>>> STAGE 1: REFINING code based on previous critique...")
            # 對話歷史現在包含任務、上一版程式碼與上一輪批判。
            # 我們指示模型依據批判進行調整。
            message_history.append(HumanMessage(content="Please refine the code using the critiques provided."))
            response = llm.invoke(message_history)
            current_code = response.content

        print("\n--- Generated Code (v" + str(i + 1) + ") ---\n" + current_code)
        message_history.append(response)  # 將產生的程式碼加入歷史

        # --- 2. 反思（Reflection）階段 ---
        print("\n>>> STAGE 2: REFLECTING on the generated code...")
        # 為反思者代理（Reflector Agent）建立特定提示。
        # 這會要求模型扮演資深程式碼審查者。
        reflector_prompt = [
            SystemMessage(content="""
                You are a senior software engineer and an expert
                in Python.
                Your role is to perform a meticulous code review.
                Critically evaluate the provided Python code based
                on the original task requirements.
                Look for bugs, style issues, missing edge cases,
                and areas for improvement.
                If the code is perfect and meets all requirements,
                respond with the single phrase 'CODE_IS_PERFECT'.
                Otherwise, provide a bulleted list of your critiques.
            """),
            HumanMessage(content=f"Original Task:\n{task_prompt}\n\nCode to Review:\n{current_code}"),
        ]

        critique_response = llm.invoke(reflector_prompt)
        critique = critique_response.content

        # --- 3. 停止條件 ---
        if "CODE_IS_PERFECT" in critique:
            print("\n--- Critique ---\nNo further critiques found. The code is satisfactory.")
            break

        print("\n--- Critique ---\n" + critique)
        # 將批判加入歷史，以便下一輪優化使用。
        message_history.append(HumanMessage(content=f"Critique of the previous code:\n{critique}"))

    print("\n" + "=" * 30 + " FINAL RESULT " + "=" * 30)
    print("\nFinal refined code after the reflection process:\n")
    print(current_code)


if __name__ == "__main__":
    run_reflection_loop()
```

這段程式碼會先設定環境、載入 API 金鑰，並初始化像 GPT-4o 這樣強大的語言模型，使用較低的溫度以獲得聚焦的輸出。核心任務透過提示定義，要求撰寫計算整數階乘的 Python 函式，包含對註解字串、邊界情況（0 的階乘）及負數輸入例外處理的具體要求。`run_reflection_loop` 函式負責協調迭代優化流程。在迴圈內，第一次迭代會根據任務提示生成初版程式碼，其後的迭代則根據上一輪的批判進行優化。另一個「反思者（Reflector）」角色（同樣由語言模型扮演，但使用不同系統提示）會擔任資深軟體工程師，依據原始任務要求批判生成的程式碼。批判會以條列問題的方式提供，或在沒有問題時回應 `CODE_IS_PERFECT`。迴圈會持續，直到批判顯示程式碼完美或達到最大迭代次數。對話歷史在每一步都維持並傳遞給語言模型，以提供生成／優化和反思（Reflection）階段的脈絡。最後，腳本會在迴圈結束後輸出最新版本的程式碼。

## 實作範例（ADK）

接下來，我們來看一個使用 Google ADK 實作的概念性範例。這段程式碼透過採用生成者－批評者（Generator-Critic）結構來展示反思（Reflection）模式，其中一個元件（生成者）產出初步結果或計劃，另一個元件（批評者）提供關鍵回饋或批判，引導生成者朝更精煉或更準確的最終輸出邁進。

```python
from google.adk.agents import SequentialAgent, LlmAgent


# 第一個代理負責生成初稿。
generator = LlmAgent(
    name="DraftWriter",
    description="Generates initial draft content on a given subject.",
    instruction="Write a short, informative paragraph about the user's subject.",
    output_key="draft_text",  # 輸出會儲存在此狀態鍵值。
)

# 第二個代理負責批判第一個代理的初稿。
reviewer = LlmAgent(
    name="FactChecker",
    description="Reviews a given text for factual accuracy and provides a structured critique.",
    instruction="""
    You are a meticulous fact-checker.
    1. Read the text provided in the state key 'draft_text'.
    2. Carefully verify the factual accuracy of all claims.
    3. Your final output must be a dictionary containing two keys:
       - "status": A string, either "ACCURATE" or "INACCURATE".
       - "reasoning": A string providing a clear explanation for your status, citing specific issues if any are found.
    """,
    output_key="review_output",  # 結構化字典會儲存在此處。
)

# SequentialAgent 確保生成者先執行，再輪到審查者。
review_pipeline = SequentialAgent(
    name="WriteAndReview_Pipeline",
    sub_agents=[generator, reviewer],
)

# 執行流程：
# 1. generator 執行 -> 將段落儲存到 state['draft_text']。
# 2. reviewer 執行 -> 讀取 state['draft_text'] 並將字典輸出儲存到 state['review_output']。
```

這段程式碼示範如何在 Google ADK 中使用串聯代理（Sequential Agent）流程來生成並審查文字。它定義了兩個 LlmAgent 實例：generator 與 reviewer。generator 代理負責就指定主題建立初稿段落，指示為撰寫一段簡短且具資訊性的內容，並將輸出儲存到狀態鍵 `draft_text`。reviewer 代理則擔任事實查核員，指示它從 `draft_text` 讀取文字並驗證其事實正確性。reviewer 的輸出是一個結構化字典，包含兩個鍵值：status 與 reasoning。status 表示文字是「ACCURATE」還是「INACCURATE」，而 reasoning 提供理由說明。這份字典會儲存在狀態鍵 `review_output`。我們建立一個名為 `review_pipeline` 的 SequentialAgent 來管理兩個代理的執行順序，確保 generator 先執行，再由 reviewer 接手。整體流程是 generator 先生成文字並儲存到狀態中，接著 reviewer 從狀態讀取文字、進行查核，並將結果（status 與 reasoning）回存到狀態。透過此管線，便能以分離的代理結構化地完成內容產生與審查。

**注意：** 若有興趣，也可使用 ADK 的 LoopAgent 實作其他替代版本。

在結尾前，值得留意的是，雖然反思（Reflection）模式大幅提升輸出品質，但也伴隨重要的取捨。這種迭代流程雖然強大，卻可能導致成本與延遲提高，因為每次優化都可能需要新的大型語言模型（LLM）呼叫，使其不適合時間敏感的應用。此外，該模式也相當耗費記憶體；隨著每次迭代，對話歷史會擴張，包含初始輸出、批判與後續優化內容。

## 一覽重點

**內容重點：** 代理的初步輸出往往不完美，可能出現錯誤、內容缺漏或無法符合複雜要求。基礎的代理工作流程缺乏讓代理自行發現並修正錯誤的機制。透過讓代理評估自己的成果，或更健全地加入專責批評者代理，可以避免初始回應無論品質如何都直接定案的情況。

**為何採用：** 反思（Reflection）模式透過引入自我修正與優化機制來解決上述問題。它建立一個回饋循環，由「產出者（Producer）」代理生成輸出，接著「批評者（Critic）」代理（或產出者本身）根據預先定義的標準進行評估。這份批判再用於生成改良版本。這種「生成、評估與優化」的迭代流程會逐步提升最終成果的品質，帶來更準確、連貫且可靠的輸出。

**經驗法則：** 當最終輸出的品質、準確性與細節比速度與成本更重要時，請使用反思（Reflection）模式。它對於產出精緻長篇內容、撰寫與除錯程式碼、制定詳盡計劃等任務特別有效。當任務需要高度客觀性或專業評估，而一般的產出者代理可能忽略時，請使用獨立的批評者代理。

**視覺摘要：**

![反思（Reflection）設計模式，自我反思（Self-Reflection）](../assets/Reflection_Design_Pattern_Self_Reflection.png)

圖 1：反思（Reflection）設計模式，自我反思（Self-Reflection）

![反思（Reflection）設計模式，產出者與批判代理（Producer and Critique Agent）](../assets/Reflection_Design_Pattern_Producer_and_Critique_Agent.png)

圖 2：反思（Reflection）設計模式，產出者與批判代理（Producer and Critique Agent）

## 重要重點

* 反思（Reflection）模式的主要優勢，是能透過迭代自我修正與優化輸出，帶來顯著更高的品質、準確性與遵守複雜指示。
* 它包含執行、評估／批判與優化的回饋循環。反思（Reflection）對於需要高品質、準確或細膩輸出的任務至關重要。
* 強而有力的實作方式是產出者－批評者（Producer-Critic）模型，由獨立代理（或提示角色）評估初始輸出。這種職責分離提升客觀性，也能提供更專業、結構化的回饋。
* 不過，這些優勢也伴隨延遲與運算成本增加的代價，同時更容易超出模型的脈絡視窗，或遭到 API 服務節流。
* 雖然完整的迭代反思（Reflection）通常需要具備狀態的工作流程（例如 LangGraph），但單一步驟的反思（Reflection）可透過 LangChain 及 LCEL 將輸出傳遞進行批判並進一步優化。
* Google ADK 可以透過串聯工作流程讓一個代理的輸出接受另一個代理批判，進而支援後續的優化步驟。
* 這個模式讓代理能進行自我修正，並隨著時間提升其表現。

## 結論

反思（Reflection）模式為代理的工作流程提供關鍵的自我修正機制，使其能在單次執行之外，透過迭代持續改進。這是透過建立一個循環來實現：系統生成輸出、根據特定標準評估，然後利用評估結果產出更精煉的成果。這項評估可以由代理自身（自我反思）或更常見且更有效的獨立批評者代理來執行，這也是此模式中的重要架構選擇。

雖然完全自動且多步驟的反思（Reflection）流程需要堅實的狀態管理架構，但其核心原則在單一「生成－批判－優化」循環中已能有效展現。作為一種控制結構，反思（Reflection）可以與其他基礎模式結合，構建更穩健且功能更強大的代理系統。

## References

Here are some resources for further reading on the Reflection pattern and related concepts:

1. Training Language Models to Self-Correct via Reinforcement Learning, [https://arxiv.org/abs/2409.12917](https://arxiv.org/abs/2409.12917)
2. LangChain Expression Language (LCEL) Documentation: [https://python.langchain.com/docs/introduction/](https://python.langchain.com/docs/introduction/)
3. LangGraph Documentation:[https://www.langchain.com/langgraph](https://www.langchain.com/langgraph)
4. Google Agent Developer Kit (ADK) Documentation (Multi-Agent Systems): [https://google.github.io/adk-docs/agents/multi-agents/](https://google.github.io/adk-docs/agents/multi-agents/)
