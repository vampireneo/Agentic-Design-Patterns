# 附錄D－使用AgentSpace建立代理（Appendix D - Building an Agent with AgentSpace）

## 概覽（Overview）

AgentSpace 是一個旨在透過把人工智能（Artificial Intelligence）融入日常工作流程（daily workflows），以促進「代理驅動企業」（agent-driven enterprise）的平臺。在核心層面，它為整個機構的數碼足跡（digital footprint）提供統一搜尋能力（unified search capability），涵蓋文件（documents）、電郵（emails）及資料庫（databases）。此系統運用先進的人工智能模型（advanced AI models），例如 Google 的 Gemini，以理解並綜合這些多元來源的資訊。

該平臺能夠建立及部署專門的人工智能代理（AI agents），可以執行複雜任務並自動化流程。這些代理（agents）並非單純的聊天機械人（chatbots）；它們可以自主推理（reason）、規劃（plan）及執行多步行動（multi-step actions）。例如，某個代理可以研究主題、編撰附有引文（citations）的報告，甚至生成音訊摘要（audio summary）。

為達成此目標，AgentSpace 建構企業知識圖譜（enterprise knowledge graph），映射人員、文件與資料之間的關係。這讓人工智能能理解脈絡（context），並提供更相關及個人化的結果。平臺亦包含名為 Agent Designer 的零代碼介面（no-code interface），讓用戶毋須深厚技術專長（technical expertise）便可建立自訂代理（custom agents）。

此外，AgentSpace 支援多代理系統（multi-agent system），不同的人工智能代理可以透過稱為 Agent2Agent（A2A）協議（protocol）的開放通訊協定互相溝通及協作。此互通性（interoperability）讓更複雜及有節奏的工作流程（workflows）得以實現。安全性（security）是基礎組件之一，設有基於角色的存取控制（role-based access controls）及資料加密（data encryption），以保護敏感的企業資訊（enterprise information）。最終，AgentSpace 致力於把智能自主系統（intelligent, autonomous systems）直接嵌入機構的營運架構（operational fabric），以提升生產力（productivity）及決策能力（decision-making）。

## 如何使用AgentSpace使用者介面建立代理（How to build an Agent with AgentSpace UI）

圖1展示如何透過在 Google Cloud 控制台（Google Cloud Console）中選取 AI 應用程式（AI Applications）以存取 AgentSpace。

![GCP: Access AgentSpace](../assets/GCP_Access_AgentSpace.png)

圖1：如何使用 Google Cloud 控制台（Google Cloud Console）存取 AgentSpace。

你的代理可以連接至多種服務，包括行事曆（Calendar）、Google 郵件（Google Mail）、Workaday、Jira、Outlook 及 Service Now（見圖2）。

![GCP: Integrate with diverse services](../assets/GCP_Integrate_with_diverse_services.png)

圖2：與多種服務整合，包括 Google 及第三方平臺（third-party platforms）。

然後代理可以使用其自身提示詞（prompt），從 Google 提供的預製提示詞圖庫（gallery of pre-made prompts）中選取，如圖3所示。

![GCP: Googles Gallery of Pre-Assembled Prompts](../assets/GCP_Googles_Gallery_of_Pre_Assembled_Prompts.png)

圖3：Google 的預先組裝提示詞圖庫（Gallery of Pre-assembled Prompts）。

另外，你亦可如圖4所示自行建立提示詞，供你的代理使用。

![GCP: Customizing the Agent's Prompt](../assets/GCP_Customizing_the_Agents_Prompt.png)

圖4：自訂代理的提示詞（Customizing the Agent's Prompt）。

AgentSpace 提供多項進階功能（advanced features），例如與資料存儲（datastores）整合以儲存自有資料（own data）、與 Google 知識圖譜（Google Knowledge Graph）或私人知識圖譜（private Knowledge Graph）整合、提供網頁介面（web interface）以將代理公開於網絡（Web），以及分析功能（analytics）以監察使用情況（monitor usage）等（見圖5）。

![GCP: AgentSpace Advanced Capabilities](../assets/GCP_AgentSpace_Advanced_Capabilities.png)

圖5：AgentSpace 的進階功能（advanced capabilities）。

完成設定後，即可使用 AgentSpace 對話介面（chat interface）（見圖6）。

![GCP: AgentSpace User Interface for initiating a chat with your Agent](../assets/GCP_AgentSpace_User_Interface_for_initiating_a_chat_with_your_Agent.png)

圖6：AgentSpace 的使用者介面（User Interface），可與你的代理展開對話。

## 結論（Conclusion）

總括而言，AgentSpace 為在機構現有數碼基礎設施（digital infrastructure）內開發及部署人工智能代理提供一個實用框架（functional framework）。系統架構（architecture）把自主推理（autonomous reasoning）及企業知識圖譜映射（enterprise knowledge graph mapping）等複雜後端流程（backend processes），連結至用於建構代理的圖形使用者介面（graphical user interface）。透過此介面，用戶可整合各類數據服務（data services），並透過提示詞（prompts）定義其運作參數（operational parameters），從而建立具情境感知（context-aware）的自動化系統（automated systems）。

此方法抽象化底層技術複雜性（technical complexity），讓用戶毋須深入的程式設計專長（programming expertise）亦可建立專門的多代理系統（specialized multi-agent systems）。其主要目標是把自動化的分析及營運能力（automated analytical and operational capabilities）直接植入工作流程（workflows），從而提升流程效率（process efficiency）並加強數據驅動分析（data-driven analysis）。為獲取實務指引，可參加實作學習模組（hands-on learning modules），例如 Google Cloud Skills Boost 上的「Build a Gen AI Agent with Agentspace」實驗室（lab），該實驗室提供結構化的技能培訓環境（structured environment for skill acquisition）。

## References

1. Create a no-code agent with Agent Designer, [https://cloud.google.com/agentspace/agentspace-enterprise/docs/agent-designer](https://cloud.google.com/agentspace/agentspace-enterprise/docs/agent-designer)
2. Google Cloud Skills Boost, [https://www.cloudskillsboost.google/](https://www.cloudskillsboost.google/)
