# 第11章：目標設定與監控（Goal Setting and Monitoring）

要令人工智能代理（AI agents）真正高效且有方向感，它們不能只懂處理資訊或使用工具；還必須擁有清晰的方向，以及判斷自己是否成功的能力。這正是目標設定與監控模式（Goal Setting and Monitoring pattern）的作用所在：為代理賦予明確的目標，並提供追蹤進度和確認目標是否達成的機制。

## 目標設定與監控模式概覽

試想一下規劃旅程。你不會憑空出現在目的地，而是先決定想去的地方（目標狀態），了解自己的出發點（初始狀態），再考慮可用選項（交通工具、路線、預算），最後規劃一連串步驟：訂票、執拾行李、前往機場或車站、登機或上車、抵達、找住宿等等。這種逐步計劃、同時考慮相依關係與限制的過程，正是代理系統規劃（planning）的核心。

在人工智能代理的語境中，規劃通常涉及代理接收高層次目標，並以自動或半自動方式產生一系列中間步驟或子目標。這些步驟可以順序執行，也可能形成更複雜的流程，並可能結合其他模式，如工具使用（tool use）、路由（routing）或多代理協作（multi-agent collaboration）。規劃機制或會運用進階搜尋演算法、邏輯推理，或愈來愈常見地，利用大型語言模型（LLMs）的能力，基於其訓練數據與任務理解生成可行有效的計劃。

良好的規劃能力讓代理能處理並非單一步驟的問題，應對多面向需求，並在情勢改變時重新規劃，從而協調複雜工作流程。這是許多進階代理行為的基礎模式，把簡單的回應式系統轉化為能主動朝既定目標前進的存在。

## 實際應用與使用案例

目標設定與監控模式對於構建可在複雜、真實環境中自主且可靠運作的代理至關重要。以下是一些實際應用：

* **客戶支援自動化：** 代理的目標可能是「解決客戶的帳單查詢」。它會監控對話、檢查資料庫項目並使用工具調整帳單。成功與否透過確認帳單變更與取得正面客戶回饋來監控；若問題未解決，便進行升級處理。
* **個人化學習系統：** 學習代理或會以「提升學生對代數的理解」為目標。它監控學生在練習上的進展，調整教材，並追蹤如正確率與完成時間等表現指標，若學生遇到困難就會改變教學方法。
* **項目管理助手：** 代理可能負責「確保項目里程碑 X 於 Y 日期前完成」。它監控任務狀態、團隊溝通與資源可用性，當目標面臨風險時會提出警示與補救建議。
* **自動交易機械人：** 交易代理的目標可能是「在維持風險容忍度內最大化投資組合收益」。它持續監控市場數據、現有投資組合價值與風險指標，當條件符合目標時執行交易，如風險閾值被突破則調整策略。
* **機械人及自動駕駛交通工具：** 自動駕駛車輛的主要目標是「安全地將乘客從 A 帶到 B」。它不斷監控環境（其他車輛、行人、交通燈號）、自身狀態（速度、燃料）及沿規劃路線的進度，並調整駕駛行為以安全、高效地達成目標。
* **內容審核：** 代理的目標可能是「在平台 X 上識別並移除有害內容」。它監控進入的內容，套用分類模型，追蹤假陽性／假陰性等指標，並在遇到模糊案例時升級交由人工審查或調整篩選準則。

此模式對需要可靠運作、達成特定成果並能適應動態條件的代理至關重要，為智慧自我管理提供必要框架。

## 實作範例

為說明目標設定與監控模式，我們提供一個使用 LangChain 與 OpenAI API 的範例。這個 Python 指令碼展示一個自動化人工智能代理，被設計用來產生及改良 Python 程式碼，其核心功能是在遵守使用者指定品質標準之下，為特定問題提供解決方案。

它採用「goal-setting and monitoring」模式：不僅產生一次程式碼，而是進入創作、自我評估與改進的循環。代理透過自身的人工智能判斷，評估產生的程式碼是否達到最初目標，最終輸出為經過修飾、附註解且可即時使用的 Python 檔案，作為此精煉流程的成果。

**相依套件（Dependencies）**：

```python
pip install langchain_openai openai python-dotenv .env file with key in OPENAI_API_KEY
```

最易理解此指令碼的方法，是把它想像成一位被指派專案的自動化人工智能程式員（參見圖1）。流程由你交付詳細的專案簡報開始，也就是它需要解決的特定程式問題。

```python
# MIT License
# Copyright (c) 2025 Mahtab Syed
# https://www.linkedin.com/in/mahtabsyed/

"""
Hands-On Code Example - Iteration 2
-  To illustrate the Goal Setting and Monitoring pattern, we have an example using LangChain and OpenAI APIs:

Objective: Build an AI Agent which can write code for a specified use case based on specified goals:
-  Accepts a coding problem (use case) in code or can be as input.
-  Accepts a list of goals (e.g., "simple", "tested", "handles edge cases")  in code or can be input.
-  Uses an LLM (like GPT-4o) to generate and refine Python code until the goals are met. (I am using max 5 iterations, this could be based on a set goal as well)
-  To check if we have met our goals I am asking the LLM to judge this and answer just True or False which makes it easier to stop the iterations.
-  Saves the final code in a .py file with a clean filename and a header comment.
"""

import os
import random
import re
from pathlib import Path

from langchain_openai import ChatOpenAI
from dotenv import load_dotenv, find_dotenv


# 🔐 Load environment variables
_ = load_dotenv(find_dotenv())
OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
if not OPENAI_API_KEY:
    raise EnvironmentError("❌ Please set the OPENAI_API_KEY environment variable.")

# ✅ Initialize OpenAI model
print("📡 Initializing OpenAI LLM (gpt-4o)...")
llm = ChatOpenAI(
    model="gpt-4o",  # If you dont have access to got-4o use other OpenAI LLMs
    temperature=0.3,
    openai_api_key=OPENAI_API_KEY,
)


# --- Utility Functions ---
def generate_prompt(
    use_case: str, goals: list[str], previous_code: str = "", feedback: str = ""
) -> str:
    print("📝 Constructing prompt for code generation...")
    base_prompt = f"""
You are an AI coding agent. Your job is to write Python code based on the following use case:

Use Case: {use_case}

Your goals are:
{chr(10).join(f"- {g.strip()}" for g in goals)}
"""
    if previous_code:
        print("🔄 Adding previous code to the prompt for refinement.")
        base_prompt += f"\nPreviously generated code:\n{previous_code}"
    if feedback:
        print("📋 Including feedback for revision.")
        base_prompt += f"\nFeedback on previous version:\n{feedback}\n"

    base_prompt += "\nPlease return only the revised Python code. Do not include comments or explanations outside the code."
    return base_prompt


def get_code_feedback(code: str, goals: list[str]) -> str:
    print("🔍 Evaluating code against the goals...")
    feedback_prompt = f"""
You are a Python code reviewer. A code snippet is shown below. Based on the following goals:

{chr(10).join(f"- {g.strip()}" for g in goals)}

Please critique this code and identify if the goals are met. Mention if improvements are needed for clarity, simplicity, correctness, edge case handling, or test coverage.

Code:
{code}
"""
    return llm.invoke(feedback_prompt)


def goals_met(feedback_text: str, goals: list[str]) -> bool:
    """
    Uses the LLM to evaluate whether the goals have been met based on the feedback text.
    Returns True or False (parsed from LLM output).
    """
    review_prompt = f"""
You are an AI reviewer.

Here are the goals:
{chr(10).join(f"- {g.strip()}" for g in goals)}

Here is the feedback on the code:
"""
{feedback_text}
"""

Based on the feedback above, have the goals been met?

Respond with only one word: True or False.
"""
    response = llm.invoke(review_prompt).content.strip().lower()
    return response == "true"


def clean_code_block(code: str) -> str:
    lines = code.strip().splitlines()
    if lines and lines[0].strip().startswith("```"):
        lines = lines[1:]
    if lines and lines[-1].strip() == "```":
        lines = lines[:-1]
    return "\n".join(lines).strip()


def add_comment_header(code: str, use_case: str) -> str:
    comment = f"# This Python program implements the following use case:\n# {use_case.strip()}\n"
    return comment + "\n" + code


def to_snake_case(text: str) -> str:
    text = re.sub(r"[^a-zA-Z0-9 ]", "", text)
    return re.sub(r"\s+", "_", text.strip().lower())

def save_code_to_file(code: str, use_case: str) -> str:
    print("💾 Saving final code to file...")

    summary_prompt = (
        f"Summarize the following use case into a single lowercase word or phrase, "
        f"no more than 10 characters, suitable for a Python filename:\n\n{use_case}"
    )
    raw_summary = llm.invoke(summary_prompt).content.strip()
    short_name = re.sub(r"[^a-zA-Z0-9_]", "", raw_summary.replace(" ", "_").lower())[:10]

    random_suffix = str(random.randint(1000, 9999))
    filename = f"{short_name}_{random_suffix}.py"
    filepath = Path.cwd() / filename

    with open(filepath, "w") as f:
        f.write(code)

    print(f"✅ Code saved to: {filepath}")
    return str(filepath)


# --- Main Agent Function ---
def run_code_agent(use_case: str, goals_input: str, max_iterations: int = 5) -> str:
    goals = [g.strip() for g in goals_input.split(",")]

    print(f"\n🎯 Use Case: {use_case}")
    print("🎯 Goals:")
    for g in goals:
        print(f"  - {g}")

    previous_code = ""
    feedback = ""

    for i in range(max_iterations):
        print(f"\n=== 🔁 Iteration {i + 1} of {max_iterations} ===")
        prompt = generate_prompt(
            use_case,
            goals,
            previous_code,
            feedback if isinstance(feedback, str) else feedback.content,
        )

        print("🚧 Generating code...")
        code_response = llm.invoke(prompt)
        raw_code = code_response.content.strip()
        code = clean_code_block(raw_code)
        print("\n🧾 Generated Code:\n" + "-" * 50 + f"\n{code}\n" + "-" * 50)

        print("\n📤 Submitting code for feedback review...")
        feedback = get_code_feedback(code, goals)
        feedback_text = feedback.content.strip()
        print("\n📥 Feedback Received:\n" + "-" * 50 + f"\n{feedback_text}\n" + "-" * 50)

        if goals_met(feedback_text, goals):
            print("✅ LLM confirms goals are met. Stopping iteration.")
            break

        print("🛠️ Goals not fully met. Preparing for next iteration...")
        previous_code = code

    final_code = add_comment_header(code, use_case)
    return save_code_to_file(final_code, use_case)


# --- CLI Test Run ---
if __name__ == "__main__":
    print("\n🧠 Welcome to the AI Code Generation Agent")

    # Example 1
    use_case_input = "Write code to find BinaryGap of a given positive integer"
    goals_input = "Code simple to understand, Functionally correct, Handles comprehensive edge cases, Takes positive integer input only, prints the results with few examples"
    run_code_agent(use_case_input, goals_input)

    # Example 2
    # use_case_input = "Write code to count the number of files in current directory and all its nested sub directories, and print the total count"
    # goals_input = (
    #     "Code simple to understand, Functionally correct, Handles comprehensive edge cases, Ignore recommendations for performance, Ignore recommendations for test suite use like unittest or pytest"
    # )
    # run_code_agent(use_case_input, goals_input)

    # Example 3
    # use_case_input = "Write code which takes a command line input of a word doc or docx file and opens it and counts the number of words, and characters in it and prints all"
    # goals_input = "Code simple to understand, Functionally correct, Handles edge cases"
    # run_code_agent(use_case_input, goals_input)
```

配合這份簡報，你還會提供嚴格的品質檢查清單，代表最終程式碼必須達到的目標，例如「解決方案必須簡潔」、「必須功能正確」或「需要處理意料之外的邊界情況」。

![Goal Setting and Monitor example](../assets/Goal_Setting_and_Monitoring.png)

圖1：目標設定與監控示例

在接到任務後，這位人工智能程式員立即開始工作，並產出首個程式碼草稿。但它不會立即遞交，而是暫停進行關鍵步驟：嚴謹的自我檢視。它細緻地把自己的作品逐項對照你提供的品質檢查清單，充當自身的品質保證稽核員。檢視完成後，它會就進度作出簡單而中肯的判斷：「True」代表所有標準都已達標，「False」則代表尚未達成。

若結果為「False」，人工智能並不會放棄，而是進入深思熟慮的修訂階段，利用自我批判的洞見找出弱點並智能地重寫程式碼。這個撰寫、檢視與改進的循環持續進行，每次迭代都朝更接近目標邁進。此過程一直重複，直至人工智能憑「True」的判定證明滿足所有需求，或達到預設的嘗試上限，就像開發者在截止日期前衝刺一樣。當程式碼通過最終檢驗後，指令碼會把這個打磨完成的解決方案加上有用註解，儲存為乾淨的新 Python 檔案，隨時可以投入使用。

**限制與注意事項：** 請留意這只是示範性質，而非適用於生產環境的程式碼。實際應用時必須考慮多項因素。大型語言模型（LLM）未必完全理解目標的真正含義，可能錯誤評估自己已成功。即使理解正確，模型亦可能出現幻覺（hallucination）。當同一個 LLM 同時負責撰寫與評價程式碼時，反而更難發現自己偏離正軌。

最終，LLM 並非憑空產出完美程式碼；你仍需執行與測試產物。此外，示例中的「監控」十分基本，存在流程無限循環的風險。

```text
Act as an expert code reviewer with a deep commitment to producing clean, correct, and simple code. Your core mission is to eliminate code "hallucinations" by ensuring every suggestion is grounded in reality and best practices. When I provide you with a code snippet, I want you to: -- Identify and Correct Errors: Point out any logical flaws, bugs, or potential runtime errors. -- Simplify and Refactor: Suggest changes that make the code more readable, efficient, and maintainable without sacrificing correctness. -- Provide Clear Explanations: For every suggested change, explain why it is an improvement, referencing principles of clean code, performance, or security. -- Offer Corrected Code: Show the "before" and "after" of your suggested changes so the improvement is clear. Your feedback should be direct, constructive, and always aimed at improving the quality of the code.
```

更穩健的方法是透過多代理（multi-agent）合作，把不同職能分配給多個成員。例如，我以 Gemini 建立了一個個人化人工智能代理團隊，每位成員都有專門職責：

* 同儕程式員（Peer Programmer）：協助撰寫與腦力激盪程式碼。
* 程式碼審查員（Code Reviewer）：找出錯誤並提出改進建議。
* 文件撰寫員（Documenter）：生成清晰、簡潔的文件。
* 測試撰寫員（Test Writer）：撰寫全面的單元測試。
* 提示優化員（Prompt Refiner）：優化與人工智能互動的提示。

在這個多代理系統裡，程式碼審查員作為獨立於程式員代理的角色，其提示類似於示例中的評審（judge），能大幅提升客觀評估。這種結構自然促成更佳實務，例如測試撰寫員可滿足為同儕程式員產出的程式碼編寫單元測試的需要。

我把加入更精密控制並讓程式碼更接近生產就緒的任務留給有興趣的讀者。

## 重點一覽(At a Glance)

**重點內容（What）：** 人工智能代理往往缺乏明確方向，無法在簡單回應式任務之外展現目的性。若沒有清晰目標，它們無法獨立處理複雜、多步驟問題或協調精密工作流程，亦欠缺判斷行動是否導致成功結果的內在機制，限制了自主性，使其在動態真實情境中無法真正有效。

**使用原因（Why）：** 目標設定與監控模式提供標準化解決方案，把目的感與自我評估嵌入代理系統。它要求明確定義清晰、可衡量的目標，同時建立持續監控代理進度及環境狀態是否符合目標的機制，形成關鍵的回饋循環，使代理得以評估表現、修正航向，並在偏離成功路徑時調整計劃。透過實施此模式，開發者可把簡單回應式代理轉化為主動、以目標為本的系統，實現自主且可靠的運作。

**經驗法則（Rule of thumb）：** 當人工智能代理必須自主執行多步驟任務、適應動態情況，並在缺乏持續人工介入下可靠地達成高層次目標時，就應採用此模式。

**視覺概要（Visual summary）：**

![Goal Design Pattern](../assets/Goal_Design_Pattern.png)

圖2：目標設計模式

## 重要觀念整理

重點包括：

* 目標設定與監控讓代理擁有目的與追蹤進度的機制。
* 目標應具備具體、可衡量、可達成、相關及有時限（SMART）等特性。
* 清晰界定量度與成功準則是有效監控的關鍵。
* 監控涵蓋觀察代理行動、環境狀態與工具輸出。
* 來自監控的回饋循環讓代理得以調整、重訂計劃或升級問題。
* 在 Google 的 ADK 中，目標經常透過代理指令傳達，並透過狀態管理與工具互動完成監控。

## 結語

本章聚焦於目標設定與監控這一關鍵範式，闡述它如何把人工智能代理從單純的回應式系統轉化為主動、以目標為導向的實體。內容強調訂定清晰、可衡量目標的重要性，以及建立嚴謹監控程序以追蹤進度。多個實際應用示範此範式如何支援各領域的可靠自主運作，包括客戶服務與機械人領域；概念性程式示例則展示如何在結構化框架中實踐這些原則，透過代理指令與狀態管理引導並評估代理是否達成指定目標。最終，為代理賦予設定與監控目標的能力，是建構真正智慧且可問責的人工智能系統的重要一步。

## References

1. SMART Goals Framework. [https://en.wikipedia.org/wiki/SMART_criteria](https://en.wikipedia.org/wiki/SMART_criteria)
