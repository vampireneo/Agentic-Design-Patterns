# 第二十一章：探索與發現（Exploration and Discovery）

本章闡述促使智能代理（intelligent agents）主動搜尋嶄新資訊、發掘未知可能性、識別「未知的未知」的設計樣式。探索與發現（exploration and discovery）不同於在既定解決方案空間內的回應式行為或最佳化，而是著重於代理主動踏入陌生領域、試驗新方法，並生成全新的知識與理解。此類樣式對於運作於開放式、複雜或急速演化領域、且無法依賴靜態知識或預編程方案的代理而言至關重要，強調代理擴展自身認知與能力的潛能。

## 實務應用與使用案例（Practical Applications & Use Cases）

AI 代理（AI agents）具備智能排序與探索能力，可在多個領域中創造價值。透過自動評估與排序潛在行動，這些代理得以穿梭複雜環境、挖掘隱藏洞見並推動創新。這種優先化探索能力讓代理能優化流程、發掘新知並生成內容。

範例：

* **科學研究自動化（Scientific Research Automation）：** 代理設計與執行實驗、分析結果並提出新假說，以發掘新材料、藥物候選物或科學原理。
* **遊戲對弈與策略生成（Game Playing and Strategy Generation）：** 代理探索遊戲狀態，發現新興策略或識別遊戲環境中的弱點（例如 AlphaGo）。
* **市場研究與趨勢洞察（Market Research and Trend Spotting）：** 代理掃描非結構化資料（社交媒體、新聞、報告），以辨識趨勢、消費者行為或市場機會。
* **安全弱點發掘（Security Vulnerability Discovery）：** 代理檢測系統或程式碼庫，以尋找安全漏洞或攻擊途徑。
* **創意內容生成（Creative Content Generation）：** 代理探索不同風格、主題或資料的組合，以創作藝術作品、音樂或文學。
* **個人化教育與訓練（Personalized Education and Training）：** AI 導師（AI tutors）按學生進度、學習風格及待改進範疇來優先安排學習路徑與內容。

Google 協同科學家（Google Co-Scientist）

Google Research 開發的 AI 協同科學家（AI co-scientist）是以 Gemini 大型語言模型（Gemini LLM）為基礎的計算型科研夥伴，協助人類科學家進行假說生成、企劃案修訂與實驗設計等研究工作。

此系統旨在回應科研上處理大量資訊、生成可測試假說與管理實驗規劃等難題。AI 協同科學家透過執行大規模資訊處理與綜合任務，協助研究人員在資料中發掘潛在關聯，藉此補強人類的認知流程，處理早期研究階段中高度計算密集的工作。

**系統架構與方法論（System Architecture and Methodology）：** AI 協同科學家的架構採用多代理框架（multi-agent framework），模擬協作與迭代流程。此設計整合具專責角色的專門代理，各自為研究目標貢獻力量，並由監督代理（supervisor agent）在非同步任務執行框架（asynchronous task execution framework）內調度與協調，以便彈性擴展計算資源。

系統的核心代理與功能如下（見圖一）：

* **生成代理（Generation agent）：** 透過文獻探索與模擬科學辯論產出初步假說。
* **反思代理（Reflection agent）：** 扮演同儕審查者，批判性評估假說的正確性、新穎性與品質。
* **排序代理（Ranking agent）：** 運用 Elo 式（Elo-based）錦標賽，透過模擬科學辯論比較、排序與優先化假說。
* **進化代理（Evolution agent）：** 持續精煉排名較高的假說，包含簡化概念、綜合理念與探索非常規推理。
* **接近度代理（Proximity agent）：** 計算接近圖（proximity graph）以分群相似想法，協助探索假說版圖。
* **後設審查代理（Meta-review agent）：** 綜整各項審查與辯論洞見，辨識共通模式並提供回饋，使系統持續改進。

系統以 Gemini 提供語言理解、推理與生成能力，並採用「測試時期計算擴展」（test-time compute scaling）機制，按需增加計算資源以迭代推理與強化輸出。系統可處理與綜整來自學術文獻、網路資料與資料庫等多元來源的資訊。

![AI Co-Scientist: Ideation to Validation](../assets/AI_Co_Scientist_Ideation_to_Validation.png)

圖一：AI 協同科學家（AI Co-Scientist）的從構想到驗證流程（由作者提供）

系統遵循模擬科學方法的「生成—辯論—進化」迭代流程。當人類科學家輸入科學問題後，系統即進入自我改進循環，持續生成、評估與精煉假說。假說會接受多代理內部評估與錦標賽式排名機制的系統化審視。

**驗證與成果（Validation and Results）：** AI 協同科學家在多項驗證研究，特別是生醫領域中展現效用，透過自動化基準測試、專家審查與端到端實驗室實驗進行評估。

**自動化與專家評估（Automated and Expert Evaluation）：** 在具挑戰性的 GPQA 基準測試中，系統內部的 Elo 評分與結果準確度高度一致，於困難的「diamond set」達到 78.4% 的前一名準確率。對逾二百項研究目標的分析顯示，擴展測試時期計算資源能持續提升假說品質（以 Elo 評分衡量）。在十五個精選難題上，AI 協同科學家的表現優於其他最先進 AI 模型與人類專家提供的「最佳猜測」方案。於小規模評估中，生物醫學專家認為該系統的輸出較其他基準模型更具新穎性與影響力。系統以美國國家衛生研究院（NIH）Specific Aims 格式撰寫的藥物再利用提案，亦獲六位腫瘤科專家評為高品質。

**端到端實驗驗證（End-to-End Experimental Validation）：**

藥物再利用（Drug Repurposing）：針對急性骨髓性白血病（acute myeloid leukemia, AML），系統提出新穎藥物候選物，其中包括在 AML 上未有任何臨床前證據的 KIRA6。後續體外實驗證實 KIRA6 與其他建議藥物可在多種 AML 細胞系中，以臨床相關濃度抑制腫瘤細胞活性。

新靶點發現（Novel Target Discovery）：系統辨識出肝纖維化的全新表觀遺傳靶點。利用人類肝臟類器官（hepatic organoids）的實驗顯示，針對所建議表觀遺傳調節因子的藥物具有顯著抗纖維化活性，其中一種藥物已獲美國食品藥物管理局（FDA）批准用於其他適應症，開啟再利用機會。

抗微生物抗藥性（Antimicrobial Resistance）：AI 協同科學家自主重現尚未公開的實驗結果。在任務要求下，系統須解釋為何某些可攜帶性細菌誘導元件（cf-PICIs）廣泛存在於多種細菌中。僅兩日內，系統排名最高的假說即為 cf-PICIs 與多樣噬菌體尾纖（phage tails）互動以擴展宿主範圍，與另一研究團隊歷經十餘年實驗後得到的創新結論相符。

**增強與限制（Augmentation and Limitations）：** AI 協同科學家的設計理念強調輔助而非完全自動化人類研究。研究人員可透過自然語言與系統互動與引導，提供回饋、提出想法並指揮 AI 的探索流程，形成「科學家在迴路內」（scientist-in-the-loop）的協作模式。然而，系統亦存在限制：其知識受限於開放取用文獻，可能錯過付費牆背後的重要既有研究；同時對負面實驗結果的掌握有限，而此類資訊鮮少發表卻對經驗豐富的科學家極為關鍵。此外，系統亦承襲基礎大型語言模型（LLMs）的侷限，包括潛在的事實錯誤或「幻覺」（hallucinations）。

**安全性（Safety）：** 安全考量至關重要，系統內建多層防護。所有研究目標在輸入時都會進行安全審查，生成的假說亦會檢測，以防系統被用於不安全或不道德研究。於 1,200 項對抗性研究目標的初步安全測試中，系統能穩健地拒絕危險輸入。為確保負責任的發展，系統透過受信測試者計劃（Trusted Tester Program）逐步開放給更多科學家，以收集實際使用回饋。

## 實作範例（Hands-On Code Example）

以下以由 Samuel Schmidgall 在 MIT 授權條款（MIT License）下開發的 Agent Laboratory 為例，展示探索與發現（Exploration and Discovery）代理的實際運作。

「Agent Laboratory」是一套自治研究工作流程框架，旨在輔助而非取代人類科研。系統運用專門大型語言模型（specialized LLMs）自動化科研流程多個階段，使人類研究者能將更多心力放在概念化與批判分析。

此框架整合 AgentRxiv——一個為自治研究代理提供成果存放、擷取與發展的平台，協助代理分享與延伸研究成果。

Agent Laboratory 透過下列階段引導研究流程：

1. **文獻回顧（Literature Review）：** 初始階段由專門大型語言模型代理自動收集並批判分析相關學術文獻，包含運用 arXiv 等外部資料庫進行辨識、綜合與分類，以建立後續階段的完整知識基礎。
2. **實驗（Experimentation）：** 此階段涵蓋協作設計實驗、資料準備、執行實驗與分析結果。代理透過整合 Python 進行程式碼生成與執行，以及使用 Hugging Face 取得模型，以自動化實驗流程。系統支援迭代式優化，代理可依即時結果調整與優化實驗程序。
3. **報告撰寫（Report Writing）：** 最終階段自動生成完整研究報告，將實驗結果與文獻回顧洞見整合，並依學術慣例結構化文件，同時結合 LaTeX 等外部工具進行專業排版與圖表製作。
4. **知識分享（Knowledge Sharing）：** AgentRxiv 平台使自治研究代理能分享、存取並共同推進科學發現，讓代理得以在既有成果上持續累積研究進展。

Agent Laboratory 的模組化架構確保計算彈性，目標是在維持人類研究者參與下，以自動化任務來提升研究生產力。

**程式碼分析（Code Analysis）：** 雖然完整程式碼解析超出本書範圍，下文提供重點並鼓勵讀者自行深入研究。

**判準機制（Judgment）：** 為模擬人類評估流程，系統採用三部分代理判準機制（tripartite agentic judgment mechanism）評量輸出。此機制部署三個自治代理，分別從特定視角檢視成果，藉此模擬人類多面向審查。此作法讓評估更周延，不再侷限單一指標，而能呈現更豐富的質性分析。

```python
class ReviewersAgent:
    def __init__(self, model="gpt-4o-mini", notes=None, openai_api_key=None):
        if notes is None:
            self.notes = []
        else:
            self.notes = notes
        self.model = model
        self.openai_api_key = openai_api_key

    def inference(self, plan, report):
        reviewer_1 = "You are a harsh but fair reviewer and expect good experiments that lead to insights for the research topic."
        review_1 = get_score(
            outlined_plan=plan,
            latex=report,
            reward_model_llm=self.model,
            reviewer_type=reviewer_1,
            openai_api_key=self.openai_api_key
        )

        reviewer_2 = "You are a harsh and critical but fair reviewer who is looking for an idea that would be impactful in the field."
        review_2 = get_score(
            outlined_plan=plan,
            latex=report,
            reward_model_llm=self.model,
            reviewer_type=reviewer_2,
            openai_api_key=self.openai_api_key
        )

        reviewer_3 = "You are a harsh but fair open-minded reviewer that is looking for novel ideas that have not been proposed before."
        review_3 = get_score(
            outlined_plan=plan,
            latex=report,
            reward_model_llm=self.model,
            reviewer_type=reviewer_3,
            openai_api_key=self.openai_api_key
        )

        return f"Reviewer #1:\n{review_1}, \nReviewer #2:\n{review_2}, \nReviewer #3:\n{review_3}"
```

評審代理的提示設計貼近人類審查者的認知框架與評量標準，引導代理從關聯性、連貫性、事實正確性與整體品質等面向進行檢視。透過模擬人類審查流程，系統力求達成近似人類判準的評估精細度。

````python
def get_score(outlined_plan, latex, reward_model_llm, reviewer_type=None, attempts=3, openai_api_key=None):
   e = str()
   for _attempt in range(attempts):
       try:

           template_instructions = """
           Respond in the following format:

           THOUGHT:
           <THOUGHT>

           REVIEW JSON:
           ```json
           <JSON>
           ```

           In <THOUGHT>, first briefly discuss your intuitions
           and reasoning for the evaluation.
           Detail your high-level arguments, necessary choices
           and desired outcomes of the review.
           Do not make generic comments here, but be specific
           to your current paper.
           Treat this as the note-taking phase of your review.

           In <JSON>, provide the review in JSON format with
           the following fields in the order:
           - "Summary": A summary of the paper content and
           its contributions.
           - "Strengths": A list of strengths of the paper.
           - "Weaknesses": A list of weaknesses of the paper.
           - "Originality": A rating from 1 to 4
             (low, medium, high, very high).
           - "Quality": A rating from 1 to 4
             (low, medium, high, very high).
           - "Clarity": A rating from 1 to 4
             (low, medium, high, very high).
           - "Significance": A rating from 1 to 4
````

在此多代理系統中，研究流程依專業角色組織，仿照學術階層以精簡流程並提升產出。

**教授代理（Professor Agent）：** 教授代理擔任主要研究主管，負責設定研究議程、界定研究問題並指派任務給其他代理。此代理負責擬定策略方向並確保與專案目標一致。

````python
class ProfessorAgent(BaseAgent):
   def __init__(self, model="gpt4omini", notes=None, max_steps=100, openai_api_key=None):
       super().__init__(model, notes, max_steps, openai_api_key)
       self.phases = ["report writing"]

   def generate_readme(self):
       sys_prompt = f"""You are {self.role_description()} \n Here is the written paper \n{self.report}. Task instructions: Your goal is to integrate all of the knowledge, code, reports, and notes provided to you and generate a readme.md for a github repository."""
       history_str = "\n".join([_[1] for _ in self.history])
       prompt = (
           f"""History: {history_str}\n{'~' * 10}\n"""
           f"Please produce the readme below in markdown:\n")
       model_resp = query_model(model_str=self.model, system_prompt=sys_prompt, prompt=prompt, openai_api_key=self.openai_api_key)
       return model_resp.replace("```markdown", "")
````

**博士後代理（PostDoc Agent）：** 博士後代理負責執行研究，包括進行文獻回顧、設計與實施實驗以及生成論文等成果。博士後代理具備撰寫與執行程式碼的能力，可實際操作實驗流程與資料分析，是主要的研究產出者。

```python
class PostdocAgent(BaseAgent):
    def __init__(self, model="gpt4omini", notes=None, max_steps=100, openai_api_key=None):
        super().__init__(model, notes, max_steps, openai_api_key)
        self.phases = ["plan formulation", "results interpretation"]

    def context(self, phase):
        sr_str = str()
        if self.second_round:
            sr_str = (
                f"The following are results from the previous experiments\n",
                f"Previous Experiment code: {self.prev_results_code}\n"
                f"Previous Results: {self.prev_exp_results}\n"
                f"Previous Interpretation of results: {self.prev_interpretation}\n"
                f"Previous Report: {self.prev_report}\n"
                f"{self.reviewer_response}\n\n\n",
            )

        if phase == "plan formulation":
            return (
                sr_str,
                f"Current Literature Review: {self.lit_review_sum}",
            )
        elif phase == "results interpretation":
            return (
                sr_str,
                f"Current Literature Review: {self.lit_review_sum}\n"
                f"Current Plan: {self.plan}\n"
                f"Current Dataset code: {self.dataset_code}\n"
                f"Current Experiment code: {self.results_code}\n"
                f"Current Results: {self.exp_results}"
            )

        return ""
```

**評審代理（Reviewer Agents）：** 評審代理對博士後代理生成的研究成果進行嚴謹評估，檢視論文與實驗結果的品質、有效性與科學嚴謹度。此評估階段模擬學術界的同儕審查流程，以確保成果在定稿前達到高標準。

**機器學習工程代理（ML Engineering Agents）：** 機器學習工程代理扮演工程師，與博士生透過對話協作撰寫程式碼。其核心任務是生成簡潔的資料前處理程式碼，並整合文獻回顧與實驗方案所提供的洞見，確保資料以適當格式為指定實驗做好準備。

```markdown
"You are a machine learning engineer being directed by a PhD student who will help you write the code, and you can interact with them through dialogue.\n"
"Your goal is to produce code that prepares the data for the provided experiment. You should aim for simple code to prepare the data, not complex code. You should integrate the provided literature review and the plan and come up with code to prepare data for this experiment.\n"
```

**軟件工程代理（SWEngineerAgents）：** 軟件工程代理指導機器學習工程代理，主要協助其為特定實驗建立簡潔的資料前處理程式碼。軟件工程代理會整合文獻回顧與實驗計畫，確保產出的程式碼既簡單又直接對應研究目標。

```markdown
"You are a software engineer directing a machine learning engineer, where the machine learning engineer will be writing the code, and you can interact with them through dialogue.\n"
"Your goal is to help the ML engineer produce code that prepares the data for the provided experiment. You should aim for very simple code to prepare the data, not complex code. You should integrate the provided literature review and the plan and come up with code to prepare data for this experiment.\n"
```

總結而言，「Agent Laboratory」是一套精密的自治科研框架，透過自動化文獻回顧、實驗與報告撰寫等關鍵階段，並促進 AI 體系的協作式知識生成，以增強人類的研究能力。系統藉由管理例行工作並維持人類監督，致力提升研究效率。

## 一覽重點（At a Glance）

**重點（What）：** AI 代理通常受限於既有知識，難以處理新情境或開放式問題。在複雜與動態環境中，這種靜態、預編程資訊不足以帶來真正的創新或發現。核心挑戰在於讓代理跨越單純最佳化，主動尋求新資訊並識別「未知的未知」，從回應式行為轉向擴展系統理解與能力的主動探索。

**原因（Why）：** 標準化方案是打造專為自治探索與發現而設計的智能代理系統（Agentic AI systems），常採多代理框架，由專門大型語言模型協作模擬科學方法流程。例如，可由不同代理負責生成假說、批判審查與進化最具潛力的概念。此結構化協作方式有助系統在龐大資訊景觀中智慧導航、設計與執行實驗，並創造真正的新知。透過自動化探索中耗費心力的任務，這些系統強化人類智慧並大幅加速發現步伐。

**經驗法則（Rule of Thumb）：** 在解決方案空間尚未完全定義的開放式、複雜或快速變動領域，應採用探索與發現樣式。此樣式適用於需要生成新假說、策略或洞見的任務，例如科學研究、市場分析與創意內容生成。其核心在於發掘「未知的未知」，而非僅僅最佳化既有流程。

**視覺摘要（Visual Summary）：**

![Exploration and Discovery Design Pattern](../assets/Exploration_and_Discovery_Design_Pattern.png)

圖二：探索與發現（Exploration and Discovery）設計樣式

## 重點摘要（Key Takeaways）

* 探索與發現能力讓 AI 代理主動追尋新資訊與可能性，對於在複雜且不斷變化的環境中導航至關重要。
* Google 協同科學家等系統展示代理如何自治生成假說與設計實驗，以補充人類科研。
* 以 Agent Laboratory 為例的多代理框架，透過自動化文獻回顧、實驗與報告撰寫等專門角色，提升研究效率。
* 最終，這些代理透過管理高度計算密集的任務來增強人類創造力與問題解決能力，從而加速創新與發現。

## 結論（Conclusion）

總結而言，探索與發現樣式是建立真正智能代理系統的核心，使其得以超越被動遵循指令，主動探索環境。這種內在的代理驅動力讓 AI 得以在複雜領域自主運作，不僅執行任務，更能獨立設定子目標以發掘新資訊。此高階代理行為最能透過多代理框架具體實現，各代理在大型協作流程中扮演主動角色。例如，Google 協同科學家包含可自治生成、辯論與進化科學假說的代理。

Agent Laboratory 等框架則進一步構築模仿人類研究團隊的代理階層，使系統得以自我管理整個發現生命週期。此樣式的核心是統籌湧現的代理行為，使系統以最少人類干預追求長期、開放式目標，從而提升人機協作，讓 AI 成為真正的代理夥伴，自主執行探索任務。藉由將主動的發現工作交付給此類代理系統，人類智慧得到大幅增幅，加速創新。同時，如此強大的代理能力亦需堅守安全與倫理監督。最終，此樣式為建構真正智能代理提供藍圖，將計算工具轉化為追求知識的自主夥伴。

## References

1. Exploration-Exploitation Dilemma**:** A fundamental problem in reinforcement learning and decision-making under uncertainty. [https://en.wikipedia.org/wiki/Exploration%E2%80%93exploitation_dilemma](https://en.wikipedia.org/wiki/Exploration%E2%80%93exploitation_dilemma)
2. Google Co-Scientist: [https://research.google/blog/accelerating-scientific-breakthroughs-with-an-ai-co-scientist/](https://research.google/blog/accelerating-scientific-breakthroughs-with-an-ai-co-scientist/)
3. Agent Laboratory: Using LLM Agents as Research Assistants [https://github.com/SamuelSchmidgall/AgentLaboratory](https://github.com/SamuelSchmidgall/AgentLaboratory)
4. AgentRxiv: Towards Collaborative Autonomous Research: [https://agentrxiv.github.io/](https://agentrxiv.github.io/)
