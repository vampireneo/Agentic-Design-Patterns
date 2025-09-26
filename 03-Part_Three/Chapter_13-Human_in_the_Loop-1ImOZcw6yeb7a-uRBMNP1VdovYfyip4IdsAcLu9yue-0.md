# 第13章：人類在循環(Human-in-the-Loop)

人類在循環(Human-in-the-Loop, HITL) 模式是一項在開發與部署代理(Agent)時至關重要的策略。它有意識地將人類認知所擅長的判斷、創意與細膩理解，與人工智能(AI)的計算能力與效率交織起來。這種策略性的整合往往不是可有可無，而是在人工智能系統(AI system)愈來愈深入關鍵決策流程之際所必需的做法。

HITL 的核心原則，是確保人工智能(AI)在倫理界限內運作、遵守安全規範，並以最佳效能達成目標。這些關切在充滿複雜性、模糊性或重大風險的領域尤為尖銳，因為人工智能(AI)出錯或誤判的後果可能十分嚴重。在這類情境下，讓人工智能系統(AI system)完全自主、毫無人類介入，往往是不智的。HITL 認清這個現實，強調即使人工智能技術(AI technology)突飛猛進，人類監督、策略投入與協同互動仍屬不可或缺。

HITL 的方法根本上圍繞著人工智能與人類智慧之間的協同。HITL 並不將人工智能(AI)視為替代人類工作的工具，而是將其定位為增強與擴展人類能力的助手。這種增強可以呈現多種形式，從自動化例行任務到提供數據驅動的洞見協助人類決策，目標是建立一個人類與人工智能代理(AI Agent)共同發揮所長的協作生態，以達成彼此單獨無法實現的成果。

在實務上，HITL 可以透過多種方式落地。一種常見方式是由人類擔任驗證者或審核者，檢視人工智能(AI)輸出以確保準確性並辨識潛在錯誤。另一種作法是人類即時引導人工智能(AI)的行為，提供回饋或立即更正。在較複雜的設置中，人類可能與人工智能(AI)並肩合作，透過互動式對話或共享介面共同解決問題或做出決策。無論採取何種實現方式，HITL 模式都凸顯維持人類掌控與監督的重要性，確保人工智能系統(AI system)始終與人類倫理、價值、目標及社會期望保持一致。

## 人類在循環模式概覽(Human-in-the-Loop Pattern Overview)

人類在循環(Human-in-the-Loop, HITL) 模式融合人工智能(AI)與人類輸入，以提升代理(Agent)的能力。這種方法承認最佳的人工智能(AI)表現往往需要自動化處理與人類洞見的結合，特別是在高度複雜或具倫理考量的情境。HITL 並非取代人類輸入，而是透過人類理解讓關鍵判斷與決策更臻完善，以增強人類能力。

HITL 包含多個關鍵面向：人類監督(Human Oversight) 涉及監察人工智能代理(AI agent)的表現與輸出（例如透過日誌審查或即時儀表板），以確保遵守指引並防止不良結果。介入與更正(Intervention and Correction) 則是在人工智能代理(AI agent)遇到錯誤或模糊情境時請求人類介入；人類操作員可以修正錯誤、補充缺漏資料或指引代理(Agent)，同時也為未來改進提供養分。學習用的人類回饋(Human Feedback for Learning) 用於精煉人工智能模型(AI model)，特別是在帶有人類回饋的強化學習(reinforcement learning with human feedback)等方法中，人類偏好會直接影響代理(Agent)的學習軌跡。決策增強(Decision Augmentation) 意指人工智能代理(AI agent)提供分析與建議，由人類作出最後決策，藉由人工智能(AI)產出的洞見提升人類決策而非追求完全自動化。人類與代理協作(Human-Agent Collaboration) 是人類與人工智能代理(AI agent)各自發揮長處的合作互動；例如代理(Agent)處理日常數據工作，而人類負責創造性問題解決或複雜談判。最後是升級政策(Escalation Policies)，即訂立明確的規程，指示代理(Agent)在何時及如何將任務升級給人類操作員，以避免代理(Agent)能力範圍之外的錯誤。

落實 HITL 模式能讓代理(Agent)用於那些無法或不被允許完全自主的敏感領域。它亦提供透過回饋循環持續改進的機制。例如在金融業，大型企業貸款的最終核准需要由人類貸款專員評估領袖特質等質化因素。同樣地，在法律領域，正義與問責的核心原則要求人類法官在涉及複雜道德判斷的重大全判上保留最終權力。

> **注意事項(Caveats)：** 儘管 HITL 好處多多，但也存在顯著限制，最主要是欠缺可擴展性。雖然人類監督具備高準確度，操作員無法處理數以百萬計的任務，因此往往需要結合自動化以求規模、再配合 HITL 以求準確度的混合模式。此外，此模式的效能高度依賴人類操作員的專業。例如，人工智能(AI)能產生軟件代碼，但只有熟練的開發者才能準確找出微妙錯誤並提供正確指引。這種對專業的需求同樣存在於利用 HITL 產生訓練數據時，因為人類標註者可能需要專門訓練，才能以提升數據品質的方式修正人工智能(AI)。最後，實施 HITL 也引發嚴重的私隱(privacy)疑慮，因為敏感資訊往往必須在提供給人類操作員之前徹底匿名化，進一步增加流程複雜度。

## 實際應用與使用案例(Practical Applications & Use Cases)

人類在循環(Human-in-the-Loop) 模式在廣泛產業與應用中至關重要，尤其是那些對準確性、安全、倫理或細膩理解要求極高的領域。

* **內容審核(Content Moderation)：** 人工智能代理(AI agent)可以快速篩選大量網絡內容以找出違規（例如仇恨言論或垃圾資訊），但模糊或臨界案例會升級給人類審核員作最終判斷，以確保細膩判斷與複雜政策的遵循。
* **自動駕駛(Autonomous Driving)：** 雖然自動駕駛汽車大多能自主處理駕駛任務，但在複雜、難以預測或危險的情況下（例如極端天氣或罕見路況），系統會交還控制權給人類駕駛員。
* **金融詐騙偵測(Financial Fraud Detection)：** 人工智能系統(AI system)可依據模式標記可疑交易，但高風險或模糊警示通常會交給人類分析員進一步調查、聯絡客戶並作出最終判定。
* **法律文件審查(Legal Document Review)：** 人工智能(AI)能迅速掃描並分類大量法律文件，找出相關條款或證據；其後由人類法律專業人士檢視人工智能(AI)的結果，確保準確、具備脈絡並符合法律含義。
* **客戶支援（複雜查詢）(Customer Support (Complex Queries))：** 聊天機械人(chatbot)可以處理例行查詢；若使用者問題過於複雜、情緒激動或需要人工智能(AI)無法提供的同理心，就會無縫交由人類支援專員接手。
* **數據標註與註解(Data Labeling and Annotation)：** 人工智能模型(AI model)往往需要大量標註數據作訓練。人類會被納入循環以準確標註影像、文本或音訊，提供人工智能(AI)學習所需的真實標準。隨著模型演進，這個過程持續進行。
* **生成式人工智能優化(Generative AI Refinement)：** 當大型語言模型(LLM)產出創意內容（例如市場文案或設計概念）時，人類編輯或設計師會審閱並潤飾結果，確保符合品牌指引、貼合目標受眾並維持品質。
* **自主網絡(Autonomous Networks)：** 人工智能系統(AI system)能透過關鍵績效指標(Key Performance Indicator, KPI)與既有模式分析警報並預測網絡問題與流量異常。不過，針對高風險警報等關鍵決策仍會升級給人類分析員，讓他們進一步調查並作出批准網絡變更與否的最終決定。

此模式示範了一種務實的人工智能實施方法。它利用人工智能(AI)提升擴展性與效率，同時維持人類監督，以確保品質、安全與倫理合規。

「人類置於監督位置(Human-on-the-loop)」是此模式的變化，讓人類專家制定總體政策，由人工智能(AI)負責即時行動以確保遵從。以下兩個例子說明其運作：

* **自動化金融交易系統(Automated financial trading system)：** 在此情境中，人類金融專家設定整體投資策略與規則。例如，專家可能訂下政策：「維持 70% 科技股與 30% 債券的投資組合、任何單一公司投資不得超過 5%、並自動賣出任何跌破買入價 10% 的股票。」人工智能(AI)即時監察股市，只要符合預設條件便立即執行交易。人工智能(AI)負責根據人類操作員較慢但更具策略性的政策，處理即時、高速的行動。
* **現代呼叫中心(Modern call center)：** 在此架構下，人類經理制定高層次的客戶互動政策。例如，經理可能規定：「任何提到『服務中斷』的來電都要即時轉接技術支援專家」，或「若客戶語氣顯示高度挫折，系統應主動提供轉接真人專員。」人工智能(AI)系統先處理最初的客戶互動，實時聆聽並解讀需求，並自動依政策即時分流或升級，而不需每個個案都由人類介入。如此一來，人工智能(AI)得以按照人類操作員較慢但策略性的指引，管理大量即時行動。

## 動手程式範例(Hands-On Code Example)

為展示人類在循環(Human-in-the-Loop) 模式，ADK 代理(Agent)可以辨識需要人類審閱的情境並啟動升級流程，讓人在代理(Agent)自主決策能力受限或需要複雜判斷時介入。這並非孤例，其他熱門框架也採用類似功能；例如 LangChain 亦提供實現此類互動的工具。

```python
from typing import Optional

from google.adk.agents import Agent
...
    # This would typically transfer to a human queue in a real system
    return {"status": "success", "message": f"Escalated {issue_type} to a human specialist."}


technical_support_agent = Agent(
    name="technical_support_specialist",
    model="gemini-2.0-flash-exp",
    instruction="""
    You are a technical support specialist for our electronics company.
    FIRST, check if the user has a support history in state["customer_info"]["support_history"].
    If they do, reference this history in your responses.

    For technical issues:
    1. Use the troubleshoot_issue tool to analyze the problem.
    2. Guide the user through basic troubleshooting steps.
    3. If the issue persists, use create_ticket to log the issue.

    For complex issues beyond basic troubleshooting:
    1. Use escalate_to_human to transfer to a human specialist.

    Maintain a professional but empathetic tone. Acknowledge the frustration technical issues can cause,
    while providing clear steps toward resolution.
    """,
    tools=[troubleshoot_issue, create_ticket, escalate_to_human],
)


def personalization_callback(
    callback_context: CallbackContext, llm_request: LlmRequest
) -> Optional[LlmRequest]:
    """Adds personalization information to the LLM request."""
    # Get customer info from state
    customer_info = callback_context.state.get("customer_info")
    if customer_info:
        customer_name = customer_info.get("name", "valued customer")
        customer_tier = customer_info.get("tier", "standard")
        recent_purchases = customer_info.get("recent_purchases", [])

        personalization_note = (
            f"\nIMPORTANT PERSONALIZATION:\n"
            f"Customer Name: {customer_name}\n"
            f"Customer Tier: {customer_tier}\n"
        )
        if recent_purchases:
            personalization_note += f"Recent Purchases: {', '.join(recent_purchases)}\n"

        if llm_request.contents:
            # Add as a system message before the first content
            system_content = types.Content(
                role="system",
                parts=[types.Part(text=personalization_note)],
            )
            llm_request.contents.insert(0, system_content)

    return None  # Return None to continue with the modified request
```

這段程式碼提供了以 Google ADK 為核心、建構在人類在循環(Human-in-the-Loop) 框架上的技術支援代理(technical support agent)藍圖。代理(Agent)作為智慧型第一線支援，透過明確指令與 `troubleshoot_issue`、`create_ticket`、`escalate_to_human` 等工具，便能處理完整支援流程。升級工具是 HITL 設計的核心，確保複雜或敏感的個案能交由人類專家。

此架構的重要特色在於其深度個人化能力，由專用回呼函式(callback function)實現。在聯絡大型語言模型(LLM)前，該函式會動態擷取代理(Agent)狀態中的客戶資料，例如姓名、等級與購買記錄，並將這些脈絡以系統訊息形式注入提示，讓代理(Agent)能提供高度客製且資訊充足的回應。藉由結合結構化流程、關鍵人類監督與動態個人化，這段程式碼展示 ADK 如何協助開發成熟且穩健的人工智能支援方案(AI support solution)。

## 一覽重點(At Glance)

**重點(What)：** 人工智能系統(AI system)，包括進階大型語言模型(LLM)，經常在需要細膩判斷、倫理推理或深刻理解複雜模糊脈絡的任務上遇到困難。在高風險環境部署完全自主的人工智能(AI)會帶來重大風險，因為錯誤可能造成嚴重的安全、財務或倫理後果。這些系統欠缺人類固有的創造力與常識推理，因此單靠自動化來處理關鍵決策，往往不智，甚至會削弱整體效能與可信度。

**原因(Why)：** 人類在循環(Human-in-the-Loop, HITL) 模式透過策略性地把人類監督納入人工智能工作流程(AI workflow)，提供標準化解決方案。這種代理導向(agentic)方法創造了一種共生夥伴關係：人工智能(AI)處理大量計算與數據工作，而人類提供關鍵驗證、回饋與介入。如此一來，HITL 確保人工智能(AI)行動符合人類價值與安全規範。這種協作框架不僅降低完全自動化的風險，也透過持續吸收人類輸入而增強系統能力，最終達成更穩健、準確且合乎倫理的成果。

**經驗法則(Rule of Thumb)：** 當要在錯誤可能造成重大安全、倫理或財務後果的領域（例如醫療保健、金融或自主系統）部署人工智能(AI)時，就應採用此模式。對於具模糊性與細膩差異，而大型語言模型(LLM)未必可靠的任務，例如內容審核或複雜客戶支援升級，也同樣適用。當目標是以高品質人類標註數據不斷改進人工智能模型(AI model)，或是優化生成式人工智能輸出(Generative AI output)以達特定品質標準時，更應採用 HITL。

**視覺摘要(Visual Summary)：**

![Human in the Loop Design Pattern](../assets/Human_in_the_Loop_Design_Pattern.png)

圖 1：人類在循環設計模式(Human in the loop design pattern)

## 核心重點(Key Takeaways)

重點包括：

* 人類在循環(Human-in-the-Loop, HITL) 將人類智慧與判斷融入人工智能工作流程(AI workflow)。
* 在複雜或高風險場景中，它對安全、倫理與效能至關重要。
* 關鍵面向包括人類監督、介入、更正、學習用的人類回饋與決策增強。
* 升級政策(Escalation policies)對代理(Agent)判斷何時交由人類承接至關重要。
* HITL 能促進負責任的人工智能部署(responsible AI deployment)與持續改進。
* 人類在循環(Human-in-the-Loop) 的主要缺點是天生缺乏可擴展性，在準確度與處理量之間形成權衡，並高度依賴具備領域專業的人才。
* 其實施帶來營運挑戰，包括必須訓練人類操作員以產生數據，以及在揭露前匿名化敏感資訊以回應私隱(privacy)疑慮。

## 結論(Conclusion)

本章探討了重要的人類在循環(Human-in-the-Loop, HITL) 模式，強調其在建立穩健、安全且合乎倫理的人工智能系統(AI system)上的角色。我們說明將人類監督、介入與回饋納入代理(Agent)工作流程，如何顯著提升其表現與可信度，尤其在複雜與敏感領域。實務應用顯示 HITL 的廣泛效益，從內容審核、醫療診斷到自動駕駛與客戶支援皆然。概念性程式範例提供了 ADK 如何透過升級機制促成人與代理(Agent)互動的概覽。隨著人工智能(AI)能力不斷進化，HITL 仍是負責任人工智能發展(responsible AI development)的基石，確保人類價值與專業始終居於智慧系統設計的核心。

## References

1. A Survey of Human-in-the-loop for Machine Learning, Xingjiao Wu, Luwei Xiao, Yixuan Sun, Junhang Zhang, Tianlong Ma, Liang He, [https://arxiv.org/abs/2108.00941](https://arxiv.org/abs/2108.00941)
