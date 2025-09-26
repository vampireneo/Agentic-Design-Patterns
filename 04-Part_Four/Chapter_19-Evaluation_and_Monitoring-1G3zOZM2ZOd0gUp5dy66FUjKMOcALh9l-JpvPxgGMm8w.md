# 第19章：評估(Evaluation)與監察(Monitoring)

本章探討讓智能代理(intelligent agents)能夠有系統地評估其表現、監察達成目標(progress toward goals)的進度，以及偵測操作異常(operational anomalies)的方法論(methodologies)。第11章講述目標設定(goal setting)與監察，而第17章專注推理機制(Reasoning mechanisms)；本章則聚焦於持續而往往是外部的量度，確保代理(agent)在效能(effectiveness)、效率(efficiency)以及對規範要求(compliance with requirements)的遵從度上維持預期水平。內容包括界定衡量指標(metrics)、建立回饋循環(feedback loops)、實施報告系統(reporting systems)，以確保代理在實際運作環境(operational environments)中的表現符合期望（見圖1）。

![監察與評估代理表現(Monitoring and Evaluating Agent Performance)](../assets/Monitoring_and_Evaluating_Agent_Performance.png)

圖1：評估與監察的最佳實務(Best practices for evaluation and monitoring)

## 實務應用與使用情境(Practical Applications & Use Cases)

最常見的應用與情境包括：

* **在線系統表現追蹤(Performance Tracking in Live Systems)：** 持續監察部署於生產環境(production environment)之代理的準確度(accuracy)、延遲(latency)與資源消耗(resource consumption)（例如客服聊天機器人(customer service chatbot)的問題解決率(resolution rate)與回應時間(response time)）。
* **代理改進的A/B測試(A/B Testing for Agent Improvements)：** 並行(systematically comparing)比較不同代理版本或策略的表現，以找出最佳方案（例如為物流代理(logistics agent)測試兩種不同的規劃演算法(planning algorithms)）。
* **合規與安全稽核(Compliance and Safety Audits)：** 產出自動化稽核報告(automated audit reports)，長期追蹤代理對道德指引(ethical guidelines)、監管要求(regulatory requirements)與安全協議(safety protocols)的遵從情況。這些報告可由人類在迴圈(human-in-the-loop)或另一個代理核實，並可在發現問題時產生關鍵績效指標(KPIs)或觸發警示(alerts)。
* **企業系統(Enterprise systems)：** 在企業系統治理Agentic AI時，需要新的管控工具(control instrument)——AI「合約(Contract)」。此動態協議(dynamic agreement)會具體化任務的目標(objectives)、規則(rules)與管控措施(controls)。
* **飄移偵測(Drift Detection)：** 長期監察代理輸出(output)的相關性(relevance)或準確性(accuracy)，偵測由於輸入資料分佈(input data distribution)改變（概念飄移(concept drift)）或環境轉變(environmental shifts)而導致的表現退化。
* **代理行為的異常偵測(Anomaly Detection in Agent Behavior)：** 辨識代理做出不尋常或出乎意料的行動，這些行動可能代表錯誤(error)、惡意攻擊(malicious attack)或新出現的不良行為(emergent undesired behavior)。
* **學習進度評估(Learning Progress Assessment)：** 針對具備學習能力的代理，追蹤其學習曲線(learning curve)、特定技能(skill)的進步，以及在不同任務或數據集(data sets)上的泛化能力(generalization capabilities)。

## 實作程式範例(Hands-On Code Example)

為AI代理建立全面的評估框架(evaluation framework)極具挑戰，複雜程度可媲美學術研究或大型出版品。這種難度源於須考慮眾多因素，例如模型表現(model performance)、用戶互動(user interaction)、倫理影響(ethical implications)及更廣泛的社會影響(broader societal impact)。然而，在實務落地方面，可將焦點收窄至最關鍵的使用情境，以確保AI代理運作高效且有效。

**代理回應評估(Agent Response Assessment)：** 這項核心流程對於評估代理輸出品質與準確度(accuracy)至關重要，旨在判斷代理是否能就給定輸入提供相關、正確、合乎邏輯、無偏與精準的資訊。評估指標可包括事實正確性(factual correctness)、流暢度(fluency)、文法精確度(grammatical precision)及是否符合用戶原意(adherence to the user's intended purpose)。

```python
def evaluate_response_accuracy(agent_output: str, expected_output: str) -> float:
    """Calculates a simple accuracy score for agent responses."""
    # This is a very basic exact match; real-world would use more sophisticated metrics
    return 1.0 if agent_output.strip().lower() == expected_output.strip().lower() else 0.0


# Example usage
agent_response = "The capital of France is Paris."
ground_truth = "Paris is the capital of France."
score = evaluate_response_accuracy(agent_response, ground_truth)
print(f"Response accuracy: {score}")
```

Python函式(function)`evaluate_response_accuracy`透過在移除前後空白後對代理輸出與期望輸出進行大小寫不敏感(case-insensitive)的精確比較(exact comparison)，計算AI代理回應的基礎準確度分數(accuracy score)。若完全相符則回傳1.0，否則為0.0，屬於二元(binary)的正確或錯誤評估。此方法雖然適合簡單檢查，但無法涵蓋例如意譯(paraphrasing)或語意等值(semantic equivalence)等變化。

問題出在比較方式。該函式採用嚴格的一字不差(character-for-character)比較。在以下示例中：

* `agent_response`: "The capital of France is Paris."
* `ground_truth`: "Paris is the capital of France."

即使移除空白並轉換成小寫，兩個字串仍不完全相同。因此，即使兩句話表達相同意思，函式仍會錯誤地回傳`0.0`的準確度。

這種直接比較無法評估語意相似度(semantic similarity)，只有在代理回應與期望輸出完全一致時才成功。更有效的評估需要進階的自然語言處理(Natural Language Processing, NLP)技術，以辨識句子之間的意義。在真實世界中，要全面評估AI代理，往往需要更複雜的指標(metrics)，例如字串相似度量(String Similarity Measures)如Levenshtein距離(Levenshtein distance)與Jaccard相似度(Jaccard similarity)、關鍵字分析(Keyword Analysis)以確認特定關鍵字是否出現、使用嵌入模型(embedding models)計算餘弦相似度(cosine similarity)的語意相似度(Semantic Similarity)、以LLM作為裁判(LLM-as-a-Judge)評估細緻的正確性與助益性(helpfulness)，以及針對檢索增強生成(RAG)的特定指標，如忠實度(faithfulness)與相關性(relevance)。

**延遲監察(Latency Monitoring)：** 針對代理行動(agent actions)的延遲監察在回應或動作速度至關重要的應用中不可或缺。此流程量度代理處理請求並產出結果所需的時間。延遲過高會損害用戶體驗(user experience)及代理整體效能，尤其在即時或互動式環境(real-time or interactive environments)中。在實務上，僅把延遲數據輸出到主控台(console)並不足夠，建議把資訊寫入持久化儲存系統(persistent storage system)。可選方案包括結構化日誌檔(structured log files，例如JSON)、時序資料庫(time-series databases，例如InfluxDB、Prometheus)、資料倉儲(data warehouses，例如Snowflake、BigQuery、PostgreSQL)，或可觀測性平台(observability platforms)，例如Datadog、Splunk、Grafana Cloud。

**追蹤LLM互動的權杖使用量(Tracking Token Usage for LLM Interactions)：** 對於由大型語言模型(LLM)驅動的代理，追蹤權杖(token)使用量對成本管理與資源配置優化(resource allocation)至關重要。LLM互動的計費常取決於處理的權杖數量（輸入與輸出），因此善用權杖可直接降低營運成本。此外，監察權杖數量有助識別提示設計(prompt engineering)或回應生成(response generation)流程中的改善空間。

```python
# This is conceptual as actual token counting depends on the LLM API
class LLMInteractionMonitor:
    def __init__(self):
        self.total_input_tokens = 0
        self.total_output_tokens = 0

    def record_interaction(self, prompt: str, response: str):
        # In a real scenario, use LLM API's token counter or a tokenizer
        input_tokens = len(prompt.split())  # Placeholder
        output_tokens = len(response.split())  # Placeholder
        self.total_input_tokens += input_tokens
        self.total_output_tokens += output_tokens
        print(f"Recorded interaction: Input tokens={input_tokens}, Output tokens={output_tokens}")

    def get_total_tokens(self):
        return self.total_input_tokens, self.total_output_tokens


# Example usage
monitor = LLMInteractionMonitor()
monitor.record_interaction("What is the capital of France?", "The capital of France is Paris.")
monitor.record_interaction("Tell me a joke.", "Why don't scientists trust atoms? Because they make up everything!")
input_t, output_t = monitor.get_total_tokens()
print(f"Total input tokens: {input_t}, Total output tokens: {output_t}")
```

本節介紹概念性的Python類別`LLMInteractionMonitor`，用於追蹤大型語言模型互動(large language model interactions)中的權杖使用量。此類別同時設有輸入與輸出權杖計數器(counters)。`record_interaction`方法會透過拆分提示(prompt)與回應(response)字串來模擬權杖計算；在實務應用中，應使用特定LLM API的權杖化工具(tokenizer)以獲得精準的權杖數量。隨著互動進行，監察器會累計總輸入與輸出權杖，`get_total_tokens`方法則提供這些累積總數，協助進行成本管理(cost management)與LLM使用優化(optimization of LLM usage)。

**以LLM作為裁判建立「助益性」客製指標(Custom Metric for "Helpfulness" using LLM-as-a-Judge)：** 評估AI代理的「助益性(helpfulness)」等主觀特質，超越了標準客觀指標的範疇。一個可行框架是使用LLM作為評審(evaluator)。此LLM-as-a-Judge方法會根據預先定義的「助益性」準則(criteria)評估另一個AI代理的輸出。藉由LLM的進階語言能力(advanced linguistic capabilities)，此方法能提供細緻且貼近人類的主觀評估，優於單純的關鍵字比對(keyword matching)或規則式(rule-based)評量。此技術雖仍在發展中，但顯示出在自動化與擴展定性評估(qualitative evaluations)方面的潛力。

```python
import os
import json
import logging
from typing import Optional

import google.generativeai as genai

# --- Configuration ---
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Set your API key as an environment variable to run this script
# For example, in your terminal: export GOOGLE_API_KEY='your_key_here'
try:
    genai.configure(api_key=os.environ["GOOGLE_API_KEY"])
except KeyError:
    logging.error("Error: GOOGLE_API_KEY environment variable not set.")
    exit(1)

# --- LLM-as-a-Judge Rubric for Legal Survey Quality ---
LEGAL_SURVEY_RUBRIC = """
 You are an expert legal survey methodologist and a critical legal reviewer. Your task is to evaluate the quality of a given legal survey question. Provide a score from 1 to 5 for overall quality, along with a detailed rationale and specific feedback.

 Focus on the following criteria:

 1.  **Clarity & Precision (Score 1-5):**
    * 1: Extremely vague, highly ambiguous, or confusing.
    * 3: Moderately clear, but could be more precise.
    * 5: Perfectly clear, unambiguous, and precise in its legal terminology (if applicable) and intent.

 2.  **Neutrality & Bias (Score 1-5):**
    * 1: Highly leading or biased, clearly influencing the respondent towards a specific answer.
    * 3: Slightly suggestive or could be interpreted as leading.
    * 5: Completely neutral, objective, and free from any leading language or loaded terms.

 3.  **Relevance & Focus (Score 1-5):**
    * 1: Irrelevant to the stated survey topic or out of scope.
    * 3: Loosely related but could be more focused.
    * 5: Directly relevant to the survey's objectives and well-focused on a single concept.

 4.  **Completeness (Score 1-5):**
    * 1: Omits critical information needed to answer accurately or provides insufficient context.
    * 3: Mostly complete, but minor details are missing.
    * 5: Provides all necessary context and information for the respondent to answer thoroughly.

 5.  **Appropriateness for Audience (Score 1-5):**
    * 1: Uses jargon inaccessible to the target audience or is overly simplistic for experts.
    * 3: Generally appropriate, but some terms might be challenging or oversimplified.
    * 5: Perfectly tailored to the assumed legal knowledge and background of the target survey audience.

 **Output Format:**
 Your response MUST be a JSON object with the following keys:
 * `overall_score`: An integer from 1 to 5 (average of criterion scores, or your holistic judgment).
 * `rationale`: A concise summary of why this score was given, highlighting major strengths and weaknesses.
 * `detailed_feedback`: A bullet-point list detailing feedback for each criterion (Clarity, Neutrality, Relevance, Completeness, Audience Appropriateness). Suggest specific improvements.
 * `concerns`: A list of any specific legal, ethical, or methodological concerns.
 * `recommended_action`: A brief recommendation (e.g., "Revise for neutrality", "Approve as is", "Clarify scope").
"""

class LLMJudgeForLegalSurvey:
    """A class to evaluate legal survey questions using a generative AI model."""

    def __init__(self, model_name: str = 'gemini-1.5-flash-latest', temperature: float = 0.2):
        """
        Initializes the LLM Judge.

        Args:
            model_name (str): The name of the Gemini model to use.
                              'gemini-1.5-flash-latest' is recommended for speed and cost.
                              'gemini-1.5-pro-latest' offers the highest quality.
            temperature (float): The generation temperature. Lower is better for deterministic evaluation.
        """
        self.model = genai.GenerativeModel(model_name)
        self.temperature = temperature

    def _generate_prompt(self, survey_question: str) -> str:
        """Constructs the full prompt for the LLM judge."""
        return f"{LEGAL_SURVEY_RUBRIC}\n\n---\n**LEGAL SURVEY QUESTION TO EVALUATE:**\n{survey_question}\n---"

    def judge_survey_question(self, survey_question: str) -> Optional[dict]:
        """
        Judges the quality of a single legal survey question using the LLM.

        Args:
            survey_question (str): The legal survey question to be evaluated.

        Returns:
            Optional[dict]: A dictionary containing the LLM's judgment, or None if an error occurs.
        """
        full_prompt = self._generate_prompt(survey_question)

        try:
            logging.info(f"Sending request to '{self.model.model_name}' for judgment...")
            response = self.model.generate_content(
                full_prompt,
                generation_config=genai.types.GenerationConfig(
                    temperature=self.temperature,
                    response_mime_type="application/json"
                )
            )

            # Check for content moderation or other reasons for an empty response.
            if not response.parts:
                safety_ratings = response.prompt_feedback.safety_ratings
                logging.error(f"LLM response was empty or blocked. Safety Ratings: {safety_ratings}")
                return None

            return json.loads(response.text)
        except json.JSONDecodeError:
            logging.error(f"Failed to decode LLM response as JSON. Raw response: {response.text}")
            return None
        except Exception as e:
            logging.error(f"An unexpected error occurred during LLM judgment: {e}")
            return None


# --- Example Usage ---
if __name__ == "__main__":
    judge = LLMJudgeForLegalSurvey()

    # --- Good Example ---
    good_legal_survey_question = """
    To what extent do you agree or disagree that current intellectual property laws in Switzerland adequately protect emerging AI-generated content, assuming the content meets the originality criteria established by the Federal Supreme Court?
    (Select one: Strongly Disagree, Disagree, Neutral, Agree, Strongly Agree)
    """
    print("\n--- Evaluating Good Legal Survey Question ---")
    judgment_good = judge.judge_survey_question(good_legal_survey_question)
    if judgment_good:
        print(json.dumps(judgment_good, indent=2))

    # --- Biased/Poor Example ---
    biased_legal_survey_question = """
    Don't you agree that overly restrictive data privacy laws like the FADP are hindering essential technological innovation and economic growth in Switzerland?
    (Select one: Yes, No)
    """
    print("\n--- Evaluating Biased Legal Survey Question ---")
    judgment_biased = judge.judge_survey_question(biased_legal_survey_question)
    if judgment_biased:
        print(json.dumps(judgment_biased, indent=2))

    # --- Ambiguous/Vague Example ---
    vague_legal_survey_question = """
    What are your thoughts on legal tech?
    """
    print("\n--- Evaluating Vague Legal Survey Question ---")
    judgment_vague = judge.judge_survey_question(vague_legal_survey_question)
    if judgment_vague:
        print(json.dumps(judgment_vague, indent=2))
```

這段Python程式碼定義了`LLMJudgeForLegalSurvey`類別，利用生成式AI模型(generative AI model)評估法律問卷問題(legal survey questions)的品質。它透過`google.generativeai`函式庫與Gemini模型互動。

其核心功能是把問卷題目連同詳盡的評分規則(rubric)一併送交模型。該規則列出五項評估準則：清晰與精準(Clarity & Precision)、中立與偏誤(Neutrality & Bias)、相關與聚焦(Relevance & Focus)、完整度(Completeness)，以及是否適合目標受眾(Appropriateness for Audience)。每項準則需給予1至5分，同時在輸出中提供詳細理由(rationale)與回饋(feedback)。程式會建構包含評分規則與待評問題的提示(prompt)。

`judge_survey_question`方法將提示送往設定好的Gemini模型，要求依指定格式回傳JSON。預期輸出的JSON包含整體分數(overall_score)、摘要理由(summary rationale)、每項準則的詳細回饋(detailed feedback)、關注事項清單(list of concerns)以及建議行動(recommended action)。類別亦處理與AI模型互動時可能出現的錯誤，例如JSON解碼問題(JSONDecodeError)或回應為空(empty responses)。範例腳本示範如何以多個法律問卷問題檢測該類別的運作，說明AI如何依據既定準則評估品質。

在總結前，先比較不同評估方法的優缺點。

| 評估方法(Evaluation Method) | 優點(Strengths) | 缺點(Weaknesses) |
| :---- | :---- | :---- |
| 人工評估(Human Evaluation) | 能捕捉細微行為(subtle behavior) | 難以擴展(scale)、成本高昂(expensive)、耗時(time-consuming)，且涉及主觀人為因素(subjective human factors)。 |
| LLM作為裁判(LLM-as-a-Judge) | 一致(consistent)、高效(efficient)、易擴展(scalable) | 可能忽略中間步驟(intermediate steps)，亦受限於LLM能力(LLM capabilities)。 |
| 自動化指標(Automated Metrics) | 可擴展(scalable)、高效率(efficient)、具客觀性(objective) | 可能無法完整捕捉全部能力(complete capabilities)。 |

## 代理軌跡(Agents trajectories)

評估代理軌跡(agent trajectories)至關重要，因為傳統軟件測試已不足夠。一般程式碼會給出可預期的通過或失敗結果，但代理運作具機率性(probabilistic)，需要同時對最終輸出及代理達成解決方案的軌跡——亦即一連串步驟(sequence of steps)—進行質性評估(qualitative assessment)。多代理系統(multi-agent systems)的評估尤其艱巨，因為它們時刻變動，需要建立超越個體表現的進階指標，以衡量溝通與團隊合作(teamwork)的成效。此外，環境本身亦非靜態，迫使評估方法（包括測試案例）必須隨時間調整。

這涉及檢視決策品質、推理過程(reasoning process)及整體成果。導入自動化評估(automated evaluations)極具價值，尤其在超越原型階段的開發。分析軌跡與工具使用(tool use)時，要檢視代理為達成目標所採用的步驟，例如工具選擇(tool selection)、策略(strategies)與任務效率(task efficiency)。以回應客戶產品查詢為例，理想軌跡可能包括判定意圖(intent determination)、使用資料庫搜尋工具(database search tool)、檢視結果(result review)以及產出報告(report generation)。代理的實際行動會與預期或標準答案(ground truth)的軌跡比較，以找出錯誤與低效。比較方法包括：完全匹配(exact match，要求與理想序列完全相同)、順序匹配(in-order match，要求正確順序但可包含額外步驟)、任意順序匹配(any-order match，正確行動不限順序並允許額外步驟)、精確率(precision，用於衡量預測行動的相關性)、召回率(recall，衡量捕捉到多少必要行動)以及單一工具使用(single-tool use，檢查是否採取特定行動)。指標選擇視代理需求而定，高風險情境可能要求完全匹配，而較具彈性的情況則可使用順序或任意順序匹配。

...

或訂錯日期。
* 他們有否制訂良好的計劃(plan)並堅守？想像計劃是先訂機票再訂酒店。如果「酒店代理(Hotel Agent)」在航班確認前就嘗試訂房，便偏離計劃。亦要檢視代理是否陷入卡關，例如無止境搜尋「完美」租車而未能進入下一步。
* 是否為正確任務挑選了合適代理？若用戶詢問旅程天氣，系統應啟用專責提供即時數據(live data)的「天氣代理(Weather Agent)」。如改由「常識代理(General Knowledge Agent)」提供籠統答案，例如「夏天通常很暖」，便選錯工具。
* 最後，新增更多代理是否能提升表現？若團隊加入新的「餐廳預約代理(Restaurant-Reservation Agent)」，整體行程規劃是否更佳、更高效？抑或引起衝突並拖慢系統，顯示延展性(scalability)問題？

## 從代理走向進階承包者(From Agents to Advanced Contractors)

 近期（Agent Companion，gulli等人）提出，AI代理將從簡單模型演進為進階「承包者(contractors)」，由常具不穩定性的機率系統轉向為複雜、高風險環境設計的、更具決定性(deterministic)及可問責(accountable)系統（見圖2）。

 今日常見的AI代理往往依賴簡短且模糊的指示，只適合簡單示範；在生產環境，含糊不清很容易導致失敗。「承包者」模型透過在用戶與AI之間建立嚴謹而正式的關係，並以清晰且雙方同意的條款為基礎，類似人類世界的法律服務協議，來解決此問題。此轉變由四大支柱構成，共同確保清晰度、可靠度以及先前自動系統難以達成的穩健執行。

第一支柱是正式化合約(Formalized Contract)。這是一份詳盡規格，作為任務唯一可信的依據，遠不止一個簡單提示(prompt)。例如，財務分析(financial analysis)的合約不會僅說「分析上一季銷售」，而會要求「撰寫一份20頁的PDF報告，分析2025年第一季歐洲市場的銷售，包含五個特定數據視覺化(data visualizations)、與2024年第一季的比較分析，以及根據所含供應鏈中斷資料集(supply chain disruptions dataset)提出風險評估(risk assessment)。」此合約會明確界定所需交付物(deliverables)、詳細規格(specifications)、可接受的資料來源(data sources)、工作範圍(scope of work)，甚至預期計算成本(computational cost)與完成時間(completion time)，使成果可客觀驗證(objectively verifiable)。

第二支柱是具動態生命週期的協商與回饋(Dynamic Lifecycle of Negotiation and Feedback)。合約不是靜態命令，而是對話的起點。承包者代理(contractor agent)可分析初始條款並進行協商。例如，若合約要求使用代理無法存取的特定專有資料來源(proprietary data source)，代理可回饋：「指定的XYZ資料庫無法存取。請提供憑證或批准使用替代的公開資料庫(public database)，但數據粒度(granularity)可能略有不同。」此協商階段亦容許代理標記模糊處或潛在風險，在執行前化解誤解，避免代價高昂的失敗，並確保最終成果完全契合用戶真正意圖。

![代理之間的合約執行示例(Contract Execution Example Among Agents)](../assets/Contract_Execution_Example_Among_Agents.png)

圖2：代理之間的合約執行示例

第三支柱是聚焦品質的迭代執行(Quality-Focused Iterative Execution)。與追求低延遲的代理不同，承包者優先確保正確性(correctness)與品質(quality)，遵循自我驗證與修正(self-validation and correction)的原則。以程式碼生成(contract for code generation)為例，代理不只撰寫程式碼，還會產生多種演算法方案(algorithmic approaches)、編譯並執行合約內指定的一系列單元測試(unit tests)，再以表現(performance)、安全性(security)、可讀性(readability)等指標為每個方案評分，最終只提交通過所有驗證標準(validation criteria)的版本。這種在符合合約規格前持續生成、審視與改進自身工作的內部循環，是建立成果可信度(trust)的關鍵。

最後一支柱是透過子合約(Subcontracts)進行層級式分解(Hierarchical Decomposition)。面對高度複雜任務，主承包者代理(primary contractor agent)可充當專案經理(project manager)，把主要目標拆解成更易管理的子任務(sub-tasks)，方法是生成新的正式子合約。例如，「打造電子商務手機應用程式(e-commerce mobile application)」的主合約，可被主代理拆成「設計UI/UX」、「開發用戶驗證模組(user authentication module)」、「建立產品資料庫架構(product database schema)」、「整合支付閘道(payment gateway)」等子合約。每份子合約都是獨立、完整的合約，具備自身交付物與規格，可交由其他專門代理處理。這種有結構的分解，讓系統能以高度組織化且可擴展的方式處理龐大而多面向的專案，標誌著AI由簡單工具蛻變為真正自主且可靠的問題解決引擎(problem-solving engine)。

最終，這個承包者框架透過把正式規格(formal specification)、協商(negotiation)與可驗證執行(verifiable execution)的原則內嵌至代理核心邏輯，重新想像AI互動。這種有條理的方法把人工智慧由充滿潛力但常不可預期的助手，提升為能自主管理複雜專案且具稽核精準度(auditable precision)的可信系統。透過解決曖昧性(ambiguity)與可靠性(reliability)的關鍵挑戰，此模型為AI在信任與問責(accountability)至上的關鍵領域(mission-critical domains)部署掃清障礙。

## Google的ADK(Google's ADK)

在結尾前，讓我們看看一個支持評估的具體框架。使用Google的ADK（見圖3）進行代理評估(agent evaluation)有三種方式：透過網頁介面(web-based UI，adk web)進行互動式評估與資料集(dataset)建立；使用pytest進行程式化整合(programmatic integration)，納入測試流程(testing pipelines)；以及使用命令列介面(command-line interface，adk eval)進行自動化評估，適用於定期構建(build)與驗證流程(verification processes)。

![Google ADK的評估支援(Evaluation Support for Google ADK)](../assets/Evaluation_Support_for_Google_ADK.png)

圖3：Google ADK的評估支援

網頁介面可建立並儲存互動式工作階段(interactive session)至既有或新建的評估集合(eval sets)，並顯示評估狀態。透過pytest整合，可呼叫`AgentEvaluator.evaluate`執行測試檔，指定代理模組(agent module)與測試檔路徑(test file path)，作為整合測試(integration tests)的一部分。

命令列介面讓自動化評估更容易，只需提供代理模組路徑與評估集合檔(eval set file)，亦可選擇指定設定檔(configuration file)或輸出詳細結果(detailed results)。若要在大型評估集合中執行特定評估，可在檔名後以逗號分隔列出名稱。

## 快速總覽(At a Glance)

**重點(What)：** Agentic系統(agentic systems)與LLM在複雜且動態的環境中運作，表現會隨時間衰退。由於其機率性與非決定性(non-deterministic)特質，傳統軟件測試不足以保證可靠性(reliability)。評估動態多代理系統是重大挑戰，因為代理與環境皆在不斷變化，需要開發具適應性的測試方法(adaptive testing methods)與進階指標，以衡量超越個別表現的協作成功(collaborative success)。部署後可能出現資料飄移(data drift)、非預期互動(unexpected interactions)、工具呼叫(tool calling)以及偏離既定目標的情況。因而必須持續評估，以量度代理的效能、效率與對操作與安全要求的遵從度。

**原因(Why)：** 標準化的評估與監察框架提供系統化方法，確保智能代理(intelligent agents)持續有良好表現。這涉及為準確度、延遲與資源消耗（例如LLM的權杖使用量）訂定清晰指標，同時採用進階技術，如分析代理軌跡以理解推理過程，並使用LLM-as-a-Judge進行細緻的定性評估。透過建立回饋循環與報告系統，此框架可促進持續改善、A/B測試，以及偵測異常或表現飄移，確保代理保持與目標一致。

**經驗法則(Rule of Thumb)：** 當你在即時生產環境(live, production environments)部署代理，且需要確保即時表現與可靠性時，應採用此模式(pattern)。此外，當需要系統性比較不同版本的代理或其底層模型以推動改進，或在需要合規、安全與倫理稽核的受監管或高風險領域運作時，此模式亦適用。若代理的表現可能隨資料或環境變化而下滑（飄移），或需要評估複雜的代理行為，包括行動順序(trajectory)與助益等主觀輸出品質，此模式同樣合適。

**視覺摘要(Visual Summary)：**

![評估與監察設計樣式(Evaluation and Monitoring Design Pattern)](../assets/Evaluation_and_Monitoring_Design_Pattern.png)

圖4：評估與監察設計樣式

## 重要重點(Key Takeaways)

* 評估智能代理需超越傳統測試，在真實環境中持續量度其效能、效率與對要求的遵從度。
* 代理評估的實務應用包括在線系統表現追蹤、A/B測試、合規稽核，以及偵測行為飄移或異常。
* 基本的代理評估聚焦回應準確度，而真實情境需要更進階的指標，例如延遲監察與針對LLM代理的權杖使用追蹤。
* 代理軌跡——代理採取的步驟序列——對評估極其關鍵，可將實際行動與理想或標準路徑比較，以找出錯誤與低效率。
* ADK透過單一測試檔為單元測試(unit testing)與完整評估集合(evalset)為整合測試(integration testing)提供結構化評估方法，並界定預期的代理行為。
* 代理評估可透過網頁介面進行互動式測試、透過pytest進行程式化CI/CD整合，或透過命令列介面操作自動化流程。
* 為讓AI在複雜、高風險任務中保持可靠，必須從簡單提示轉向正式「合約」，精準界定可驗證的交付物與範圍，使代理能協商、釐清模糊處並反覆驗證自身工作，從不可預期的工具蛻變為可問責且值得信賴的系統。

## 結論(Conclusions)

總而言之，要有效評估AI代理，必須超越簡單的準確度檢查，改採對其在動態環境中的表現進行持續且多面向的評估。這包含實際監察延遲與資源消耗等指標，以及透過軌跡深入分析代理的決策過程(decision-making process)。對於助益性等細緻特質，LLM-as-a-Judge等創新方法愈見重要；同時，Google的ADK提供結構化工具以支援單元與整合測試。面對多代理系統，挑戰進一步提升，焦點必須轉向評估協作成功(collaborative success)與有效合作。

為確保在關鍵應用中維持可靠性(reliability)，現正從簡單、依賴提示的代理轉向受正式協議拘束的進階「承包者」。這些承包者代理以明確且可驗證的條款運作，能夠協商、拆解任務並自我驗證，以達到嚴格的品質標準。此結構化方法把代理由不可預期的工具，轉化為能應付複雜高風險任務的可問責系統。最終，這種演進對於在關鍵領域部署高端agentic AI，建立所需的信任(trust)至關重要。

## References

Relevant research includes:

1. ADK Web: [https://github.com/google/adk-web](https://github.com/google/adk-web)
2. ADK Evaluate: [https://google.github.io/adk-docs/evaluate/](https://google.github.io/adk-docs/evaluate/)
3. Survey on Evaluation of LLM-based Agents, [https://arxiv.org/abs/2503.16416](https://arxiv.org/abs/2503.16416)
4. Agent-as-a-Judge: Evaluate Agents with Agents, [https://arxiv.org/abs/2410.10934](https://arxiv.org/abs/2410.10934)
5. Agent Companion, gulli et al: [https://www.kaggle.com/whitepaper-agent-companion](https://www.kaggle.com/whitepaper-agent-companion)
