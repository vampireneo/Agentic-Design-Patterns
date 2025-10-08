# 詞彙索引（Index of Terms）

## 基礎概念

- **提示（Prompt）：** 用戶給人工智能（AI）模型的輸入，通常是問題、指令或者陳述，用來引導模型回應。提示的質素和結構深刻影響輸出，因此提示工程（prompt engineering）是有效使用 AI 的關鍵技能。
- **脈絡視窗（Context Window）：** 模型一次可以處理的最大權重數，包括輸入和輸出。視窗大小是重要限制；視窗之外的資訊會被忽略，而較大視窗可以支持更複雜的對話和文件分析。
- **脈絡內學習（In-Context Learning）：** 模型透過提示當中的示例，即時學會新任務，毋須重新訓練。這種能力令通用模型可以即場適應無數具體任務。
- **零樣本、單樣本、少樣本提示（Zero-Shot, One-Shot & Few-Shot Prompting）：** 模型在提示中獲得零個、一個或少量示例，以指引回應。示例越多，通常愈能理解用戶意圖，同時提升特定任務的準確度。
- **多模態（Multimodality）：** AI 理解並處理多種數據類型（文字、圖像、音訊）的能力，可帶來更全面、人性化的互動，例如描述圖片或回應語音提問。
- **錨定（Grounding）：** 將模型輸出連結到可驗證的現實資訊來源，以確保事實準確、減少幻覺（hallucination）。常見手法包括檢索增強生成（RAG），令 AI 系統更可信。

## 核心 AI 模型架構

- **Transformer：** 現代大型語言模型（Large Language Model，LLM）的基礎神經網絡架構，自注意力（self-attention）是其核心創新，可高效處理長文本並掌握複雜關是。
- **循環神經網絡（Recurrent Neural Network，RNN）：** Transformer 出現之前的基礎架構，以序列方式處理資訊，透過迴圈保留過往輸入的「記憶」，適合文本和語音處理。
- **專家混合（Mixture of Experts，MoE）：** 利用「路由器」網絡動態選擇少量「專家」子網絡處理輸入，令模型可以擁有大量參數同時控制計算成本。
- **擴散模型（Diffusion Models）：** 擅長生成高質素圖像的生成式模型，先向數據加入噪音，再訓練模型逐步逆轉，最終由隨機起點生成新資料。
- **Mamba：** 採用選擇性狀態空間模型（Selective State Space Model，SSM）的新興架構，能高效率處理特長序列，聚焦相關資訊、濾走噪音，被視為 Transformer 的潛在替代方案。

## 大型語言模型開發生命週期

- 強大語言模型的開發通常分為三個階段。首先是預訓練（Pre-training），利用龐大網絡文本建立基礎模型，學習語言、推理和世界知識。其後進行微調（Fine-tuning），在規模較細、任務導向的數據集上專門化模型能力。最後是對齊（Alignment），調整模型行為，確保輸出有用、無害並符合人類價值。
- **預訓練技巧（Pre-training Techniques）：** 包括因果語言建模（Causal Language Modeling，CLM）——預測句子下一個詞；遮罩語言建模（Masked Language Modeling，MLM）——填補刻意遮蓋的詞；去噪目標（Denoising Objectives）——還原受破壞輸入；對比式學習（Contrastive Learning）——分辨相似與差異數據；以及下一句預測（Next Sentence Prediction，NSP）——判斷兩句是否合理相連。
- **微調技巧（Fine-tuning Techniques）：** 指將通用預訓練模型以較細、專門數據集調整至特定任務。常見方法是監督式微調（Supervised Fine-Tuning，SFT），利用已標註示例訓練；指令微調（Instruction Tuning）專注提升模型跟指令能力；參數高效微調（Parameter-Efficient Fine-Tuning，PEFT）例如 LoRA（Low-Rank Adaptation）和 QLoRA，只更新少量參數；檢索增強生成（RAG）則在微調或推理期間連接外部知識。
- **對齊與安全技巧（Alignment & Safety Techniques）：** 確保模型行為符合人類價值並保持安全。代表性方法是源自人類回饋的強化學習（Reinforcement Learning from Human Feedback，RLHF），以人類偏好訓練的獎勵模型指導學習，常配合近端策略優化（Proximal Policy Optimization，PPO）。其他方法包括直接偏好優化（Direct Preference Optimization，DPO）及 Kahneman-Tversky Optimization（KTO），並以護欄（Guardrails）作最後安全層，即時過濾輸出、阻擋危險行為。

## 提升 AI 代理能力

- AI 代理是能感知環境、為達目標而自主行動的系統，透過穩健的推理框架可以大幅提升效能。
- **思維鏈（Chain of Thought，CoT）：** 鼓勵模型逐步解釋推理，再提供最終答案，有助提升複雜推理的準確度。
- **思維樹（Tree of Thoughts，ToT）：** 高階推理框架，代理同時探索多條推理路線，自我評估並選擇最具前景的方向。
- **ReAct（Reason and Act）：** 將推理與行動串成循環，代理先思考，再透過工具行動，再根據觀察調整思考，特別適合複雜任務。
- **規劃（Planning）：** 將高層目標拆解成可管理的子任務，按次序制定執行計劃，應付多步驟任務。
- **深度研究（Deep Research）：** 代理自主深入探索主題，反覆搜尋、綜合結果並發掘新問題，建立全面理解。
- **評論模型（Critique Model）：** 專門評估另一個 AI 模型輸出的系統，猶如自動化評論員，協助找錯、改進推理、確保成果質素。

## 詞彙索引

本索引由 Gemini Pro 2.5 生成，尾段附上提示和推理步驟，展示節省時間的成效和教育用途。

### A

- A/B 測試（A/B Testing）——第 3 章〈並行化〉
- 行動選擇（Action Selection）——第 20 章〈優先次序〉
- 適應（Adaptation）——第 9 章〈學習與適應〉
- 自適應任務分配（Adaptive Task Allocation）——第 16 章〈資源感知優化〉
- 自適應工具使用與選擇（Adaptive Tool Use & Selection）——第 16 章〈資源感知優化〉
- 代理（Agent）——〈甚麼令一個 AI 系統成為代理？〉
- 代理－電腦介面（Agent-Computer Interfaces，ACI）——附錄 B
- 代理驅動經濟（Agent-Driven Economy）——〈甚麼令一個 AI 系統成為代理？〉
- 作為工具的代理（Agent as a Tool）——第 7 章〈多代理協作〉
- 代理卡（Agent Cards）——第 15 章〈代理間通訊（A2A）〉
- 代理開發套件（Agent Development Kit，ADK）——第 2、3、4、5、7、8、12、13、15、16、19 章；附錄 C
- 代理探索（Agent Discovery）——第 15 章〈代理間通訊（A2A）〉
- 代理軌跡（Agent Trajectories）——第 19 章〈評估與監控〉
- 代理型設計模式（Agentic Design Patterns）——〈導言〉
- 代理型 RAG（Agentic RAG）——第 14 章〈知識檢索（RAG）〉
- 代理型系統（Agentic Systems）——〈導言〉
- AI 共和科學家（AI Co-scientist）——第 21 章〈探索與發現〉
- 對齊（Alignment）——〈詞彙表〉
- AlphaEvolve——第 9 章〈學習與適應〉
- 類比（Analogies）——附錄 A
- 異常偵測（Anomaly Detection）——第 19 章〈評估與監控〉
- Anthropic Claude 4 系列——附錄 B
- Anthropic Computer Use——附錄 B
- API 互動（API Interaction）——第 10 章〈模型上下文協議（MCP）〉
- 成品（Artifacts）——第 15 章〈代理間通訊（A2A）〉
- 非和步輪詢（Asynchronous Polling）——第 15 章〈代理間通訊（A2A）〉
- 稽核日誌（Audit Logs）——第 15 章〈代理間通訊（A2A）〉
- 自動化指標（Automated Metrics）——第 19 章〈評估與監控〉
- 自動提示工程（Automatic Prompt Engineering，APE）——附錄 A
- 自主性（Autonomy）——〈導言〉
- A2A（Agent-to-Agent）——第 15 章〈代理間通訊（A2A）〉

### B

- 行為限制（Behavioral Constraints）——第 18 章〈護欄與安全模式〉
- 瀏覽器使用（Browser Use）——附錄 B

### C

- 回呼（Callbacks）——第 18 章〈護欄與安全模式〉
- 因果語言建模（Causal Language Modeling，CLM）——〈詞彙表〉
- 辯論鏈（Chain of Debates，CoD）——第 17 章〈推理技術〉
- 思維鏈（Chain of Thought，CoT）——第 17 章〈推理技術〉；附錄 A
- 聊天機械人（Chatbots）——第 8 章〈記憶體管理〉
- ChatMessageHistory——第 8 章〈記憶體管理〉
- 檢查點與回滾（Checkpoint and Rollback）——第 18 章〈護欄與安全模式〉
- 切分（Chunking）——第 14 章〈知識檢索（RAG）〉
- 清晰與具體（Clarity and Specificity）——附錄 A
- 客戶端代理（Client Agent）——第 15 章〈代理間通訊（A2A）〉
- 程式碼生成（Code Generation）——第 1 章〈提示鏈結〉、第 4 章〈反思〉
- 程式碼提示（Code Prompting）——附錄 A
- 辯論鏈（CoD）——第 17 章〈推理技術〉
- 思維鏈（CoT）——第 17 章〈推理技術〉；附錄 A
- 協作（Collaboration）——第 7 章〈多代理協作〉
- 合規（Compliance）——第 19 章〈評估與監控〉
- 精簡（Conciseness）——附錄 A
- 內容生成（Content Generation）——第 1 章〈提示鏈結〉、第 4 章〈反思〉
- 脈絡工程（Context Engineering）——第 1 章〈提示鏈結〉
- 脈絡視窗（Context Window）——〈詞彙表〉
- 脈絡修剪與摘要（Contextual Pruning & Summarization）——第 16 章〈資源感知優化〉
- 脈絡提示（Contextual Prompting）——附錄 A
- 承包模型（Contractor Model）——第 19 章〈評估與監控〉
- ConversationBufferMemory——第 8 章〈記憶體管理〉
- 對話代理（Conversational Agents）——第 1 章〈提示鏈結〉、第 4 章〈反思〉
- 成本敏感探索（Cost-Sensitive Exploration）——第 16 章〈資源感知優化〉
- CrewAI——第 3、5、6、7、18 章；附錄 C
- 評論代理（Critique Agent）——第 16 章〈資源感知優化〉
- 評論模型（Critique Model）——〈詞彙表〉
- 客戶支援（Customer Support）——第 13 章〈人類在迴圈中〉

### D

- 數據擷取（Data Extraction）——第 1 章〈提示鏈結〉
- 數據標註（Data Labeling）——第 13 章〈人類在迴圈中〉
- 資料庫整合（Database Integration）——第 10 章〈模型上下文協議（MCP）〉
- DatabaseSessionService——第 8 章〈記憶體管理〉
- 辯論與共識（Debate and Consensus）——第 7 章〈多代理協作〉
- 決策增強（Decision Augmentation）——第 13 章〈人類在迴圈中〉
- 拆解（Decomposition）——附錄 A
- 深度研究（Deep Research）——第 6 章〈規劃〉、第 17 章〈推理技術〉；〈詞彙表〉
- 分隔符（Delimiters）——附錄 A
- 去噪目標（Denoising Objectives）——〈詞彙表〉
- 相依性（Dependencies）——第 20 章〈優先次序〉
- 擴散模型（Diffusion Models）——〈詞彙表〉
- 直接偏好優化（Direct Preference Optimization，DPO）——第 9 章〈學習與適應〉
- 可探索性（Discoverability）——第 10 章〈模型上下文協議（MCP）〉
- 漂移偵測（Drift Detection）——第 19 章〈評估與監控〉
- 動態模型切換（Dynamic Model Switching）——第 16 章〈資源感知優化〉
- 動態重新排序（Dynamic Re-prioritization）——第 20 章〈優先次序〉

### E

- 嵌入表示（Embeddings）——第 14 章〈知識檢索（RAG）〉
- 體現（Embodiment）——〈甚麼令一個 AI 系統成為代理？〉
- 節能部署（Energy-Efficient Deployment）——第 16 章〈資源感知優化〉
- 情節記憶（Episodic Memory）——第 8 章〈記憶體管理〉
- 錯誤偵測（Error Detection）——第 12 章〈例外處理與復原〉
- 錯誤處理（Error Handling）——第 12 章〈例外處理與復原〉
- 升級政策（Escalation Policies）——第 13 章〈人類在迴圈中〉
- 評估（Evaluation）——第 19 章〈評估與監控〉
- 例外處理（Exception Handling）——第 12 章〈例外處理與復原〉
- 專家團隊（Expert Teams）——第 7 章〈多代理協作〉
- 探索與發現（Exploration and Discovery）——第 21 章〈探索與發現〉
- 外部審核 API（External Moderation APIs）——第 18 章〈護欄與安全模式〉

### F

- 分解認知（Factored Cognition）——附錄 A
- FastMCP——第 10 章〈模型上下文協議（MCP）〉
- 容錯（Fault Tolerance）——第 18 章〈護欄與安全模式〉
- 少樣本學習（Few-Shot Learning）——第 9 章〈學習與適應〉
- 少樣本提示（Few-Shot Prompting）——附錄 A
- 微調（Fine-tuning）——〈詞彙表〉
- 正式合約（Formalized Contract）——第 19 章〈評估與監控〉
- 函數呼叫（Function Calling）——第 5 章〈工具使用〉；附錄 A

### G

- Gemini Live——附錄 B
- Gems——附錄 A
- 生成式媒體協調（Generative Media Orchestration）——第 10 章〈模型上下文協議（MCP）〉
- 目標設定（Goal Setting）——第 11 章〈目標設定與監控〉
- 辯論圖（Graph of Debates，GoD）——第 17 章〈推理技術〉
- Google 代理開發套件（Google Agent Development Kit，ADK）——第 2、3、4、5、7、8、12、13、15、16、19 章；附錄 C
- Google Co-Scientist——第 21 章〈探索與發現〉
- Google DeepResearch——第 6 章〈規劃〉
- Google Project Mariner——附錄 B
- 優雅退化（Graceful Degradation）——第 12 章〈例外處理與復原〉、第 16 章〈資源感知優化〉
- 辯論圖（Graph of Debates，GoD）——第 17 章〈推理技術〉
- 錨定（Grounding）——〈詞彙表〉
- 護欄（Guardrails）——第 18 章〈護欄與安全模式〉

### H

- Haystack——附錄 C
- 層級式拆解（Hierarchical Decomposition）——第 19 章〈評估與監控〉
- 層級結構（Hierarchical Structures）——第 7 章〈多代理協作〉
- HITL（Human-in-the-Loop）——第 13 章〈人類在迴圈中〉
- 人類在迴圈中（Human-in-the-Loop，HITL）——第 13 章〈人類在迴圈中〉
- 人類在迴圈上方（Human-on-the-loop）——第 13 章〈人類在迴圈中〉
- 人類監督（Human Oversight）——第 13 章〈人類在迴圈中〉、第 18 章〈護欄與安全模式〉

### I

- 脈絡內學習（In-Context Learning）——〈詞彙表〉
- InMemoryMemoryService——第 8 章〈記憶體管理〉
- InMemorySessionService——第 8 章〈記憶體管理〉
- 輸入驗證／清理（Input Validation/Sanitization）——第 18 章〈護欄與安全模式〉
- 以指令為先（Instructions Over Constraints）——附錄 A
- 代理間通訊（Inter-Agent Communication，A2A）——第 15 章〈代理間通訊（A2A）〉
- 介入與修正（Intervention and Correction）——第 13 章〈人類在迴圈中〉
- 物聯網裝置控制（IoT Device Control）——第 10 章〈模型上下文協議（MCP）〉
- 反覆提示／優化（Iterative Prompting / Refinement）——附錄 A

### J

- 監獄破防（Jailbreaking）——第 18 章〈護欄與安全模式〉

### K

- Kahneman-Tversky Optimization（KTO）——〈詞彙表〉
- 知識檢索（Knowledge Retrieval，RAG）——第 14 章〈知識檢索（RAG）〉

### L

- LangChain——第 1、2、3、4、5、8、20 章；附錄 C
- LangGraph——第 1、2、3、4、5、8 章；附錄 C
- 延遲監測（Latency Monitoring）——第 19 章〈評估與監控〉
- 學習式資源分配策略（Learned Resource Allocation Policies）——第 16 章〈資源感知優化〉
- 學習與適應（Learning and Adaptation）——第 9 章〈學習與適應〉
- 以 LLM 作評審（LLM-as-a-Judge）——第 19 章〈評估與監控〉
- LlamaIndex——附錄 C
- LoRA（Low-Rank Adaptation）——〈詞彙表〉
- 低秩調整（Low-Rank Adaptation，LoRA）——〈詞彙表〉

### M

- Mamba——〈詞彙表〉
- 遮罩語言建模（Masked Language Modeling，MLM）——〈詞彙表〉
- MASS（Multi-Agent System Search）——第 17 章〈推理技術〉
- MCP（Model Context Protocol）——第 10 章〈模型上下文協議（MCP）〉
- 記憶體管理（Memory Management）——第 8 章〈記憶體管理〉
- 基於記憶體的學習（Memory-Based Learning）——第 9 章〈學習與適應〉
- MetaGPT——附錄 C
- Microsoft AutoGen——附錄 C
- 專家混合（Mixture of Experts，MoE）——〈詞彙表〉
- 模型上下文協議（Model Context Protocol，MCP）——第 10 章〈模型上下文協議（MCP）〉
- 模組化（Modularity）——第 18 章〈護欄與安全模式〉
- 監控（Monitoring）——第 11 章〈目標設定與監控〉、第 19 章〈評估與監控〉
- 多代理協作（Multi-Agent Collaboration）——第 7 章〈多代理協作〉
- 多代理系統搜尋（Multi-Agent System Search，MASS）——第 17 章〈推理技術〉
- 多模態（Multimodality）——〈詞彙表〉
- 多模態提示（Multimodal Prompting）——附錄 A

### N

- 負面示例（Negative Examples）——附錄 A
- 下一句預測（Next Sentence Prediction，NSP）——〈詞彙表〉

### O

- 可觀測性（Observability）——第 18 章〈護欄與安全模式〉
- 單樣本提示（One-Shot Prompting）——附錄 A
- 線上學習（Online Learning）——第 9 章〈學習與適應〉
- OpenAI Deep Research API——第 6 章〈規劃〉
- OpenEvolve——第 9 章〈學習與適應〉
- OpenRouter——第 16 章〈資源感知優化〉
- 輸出過濾／後處理（Output Filtering/Post-processing）——第 18 章〈護欄與安全模式〉

### P

- PAL（Program-Aided Language Models）——第 17 章〈推理技術〉
- 並行化（Parallelization）——第 3 章〈並行化〉
- 並行化與分佈式計算意識（Parallelization & Distributed Computing Awareness）——第 16 章〈資源感知優化〉
- 參數高效微調（Parameter-Efficient Fine-Tuning，PEFT）——〈詞彙表〉
- PEFT（Parameter-Efficient Fine-Tuning）——〈詞彙表〉
- 效能追蹤（Performance Tracking）——第 19 章〈評估與監控〉
- 人物設定模式（Persona Pattern）——附錄 A
- 個人化（Personalization）——〈甚麼令一個 AI 系統成為代理？〉
- 規劃（Planning）——第 6 章〈規劃〉；〈詞彙表〉
- 優先次序（Prioritization）——第 20 章〈優先次序〉
- 最小特權原則（Principle of Least Privilege）——第 18 章〈護欄與安全模式〉
- 主動資源預測（Proactive Resource Prediction）——第 16 章〈資源感知優化〉
- 程序性記憶（Procedural Memory）——第 8 章〈記憶體管理〉
- Program-Aided Language Models（PAL）——第 17 章〈推理技術〉
- Project Astra——附錄 B
- 提示（Prompt）——〈詞彙表〉
- 提示鏈結（Prompt Chaining）——第 1 章〈提示鏈結〉
- 提示工程（Prompt Engineering）——附錄 A
- 近端策略優化（Proximal Policy Optimization，PPO）——第 9 章〈學習與適應〉
- 推播通知（Push Notifications）——第 15 章〈代理間通訊（A2A）〉

### Q

- QLoRA——〈詞彙表〉
- 注重品質的反覆執行（Quality-Focused Iterative Execution）——第 19 章〈評估與監控〉

### R

- 檢索增強生成（Retrieval-Augmented Generation，RAG）——第 8 章〈記憶體管理〉、第 14 章〈知識檢索（RAG）〉、附錄 A
- ReAct（Reason and Act）——第 17 章〈推理技術〉、附錄 A、〈詞彙表〉
- 推理（Reasoning）——第 17 章〈推理技術〉
- 以推理為本的資訊擷取（Reasoning-Based Information Extraction）——第 10 章〈模型上下文協議（MCP）〉
- 復原（Recovery）——第 12 章〈例外處理與復原〉
- 循環神經網絡（Recurrent Neural Network，RNN）——〈詞彙表〉
- 反思（Reflection）——第 4 章〈反思〉
- 強化學習（Reinforcement Learning）——第 9 章〈學習與適應〉
- 人類回饋強化學習（Reinforcement Learning from Human Feedback，RLHF）——〈詞彙表〉
- 可驗證獎勵強化學習（Reinforcement Learning with Verifiable Rewards，RLVR）——第 17 章〈推理技術〉
- 遠端代理（Remote Agent）——第 15 章〈代理間通訊（A2A）〉
- 請求／回應（輪詢）（Request/Response，Polling）——第 15 章〈代理間通訊（A2A）〉
- 資源感知優化（Resource-Aware Optimization）——第 16 章〈資源感知優化〉
- 檢索增強生成（Retrieval-Augmented Generation，RAG）——第 8 章、第 14 章、附錄 A
- RLHF（Reinforcement Learning from Human Feedback）——〈詞彙表〉
- RLVR（Reinforcement Learning with Verifiable Rewards）——第 17 章〈推理技術〉
- RNN（Recurrent Neural Network）——〈詞彙表〉
- 角色提示（Role Prompting）——附錄 A
- 路由代理（Router Agent）——第 16 章〈資源感知優化〉
- 路由（Routing）——第 2 章〈路由〉

### S

- 安全（Safety）——第 18 章〈護欄與安全模式〉
- 推理規模定律（Scaling Inference Law）——第 17 章〈推理技術〉
- 排程（Scheduling）——第 20 章〈優先次序〉
- 自我一致性（Self-Consistency）——附錄 A
- 自我修正（Self-Correction）——第 4 章〈反思〉、第 17 章〈推理技術〉
- 自我改進編碼代理（Self-Improving Coding Agent，SICA）——第 9 章〈學習與適應〉
- 自我精煉（Self-Refinement）——第 17 章〈推理技術〉
- Semantic Kernel——附錄 C
- 語意記憶（Semantic Memory）——第 8 章〈記憶體管理〉
- 語意相似度（Semantic Similarity）——第 14 章〈知識檢索（RAG）〉
- 職責分離（Separation of Concerns）——第 18 章〈護欄與安全模式〉
- 連續交接（Sequential Handoffs）——第 7 章〈多代理協作〉
- 伺服器推送事件（Server-Sent Events，SSE）——第 15 章〈代理間通訊（A2A）〉
- 工作階段（Session）——第 8 章〈記憶體管理〉
- SICA（Self-Improving Coding Agent）——第 9 章〈學習與適應〉
- SMART 目標——第 11 章〈目標設定與監控〉
- 狀態（State）——第 8 章〈記憶體管理〉
- 狀態回滾（State Rollback）——第 12 章〈例外處理與復原〉
- 回頭提示（Step-Back Prompting）——附錄 A
- 串流更新（Streaming Updates）——第 15 章〈代理間通訊（A2A）〉
- 結構化日誌（Structured Logging）——第 18 章〈護欄與安全模式〉
- 結構化輸出（Structured Output）——第 1 章〈提示鏈結〉、附錄 A
- SuperAGI——附錄 C
- 監督式微調（Supervised Fine-Tuning，SFT）——〈詞彙表〉
- 監督式學習（Supervised Learning）——第 9 章〈學習與適應〉
- 系統提示（System Prompting）——附錄 A

### T

- 任務評估（Task Evaluation）——第 20 章〈優先次序〉
- 文字相似度（Text Similarity）——第 14 章〈知識檢索（RAG）〉
- 權重使用量（Token Usage）——第 19 章〈評估與監控〉
- 工具使用（Tool Use）——第 5 章〈工具使用〉、附錄 A
- 工具使用限制（Tool Use Restrictions）——第 18 章〈護欄與安全模式〉
- ToT（Tree of Thoughts）——第 17 章〈推理技術〉、附錄 A、〈詞彙表〉
- Transformer——〈詞彙表〉
- 思維樹（Tree of Thoughts，ToT）——第 17 章〈推理技術〉、附錄 A、〈詞彙表〉

### U

- 無監督學習（Unsupervised Learning）——第 9 章〈學習與適應〉
- 用戶人物誌（User Persona）——附錄 A

### V

- 驗證（Validation）——第 3 章〈並行化〉
- 向量搜尋（Vector Search）——第 14 章〈知識檢索（RAG）〉
- VertexAiRagMemoryService——第 8 章〈記憶體管理〉
- VertexAiSessionService——第 8 章〈記憶體管理〉
- 感覺編碼（Vibe Coding）——附錄 B
- 視覺感知（Visual Perception）——附錄 B

### W

- Webhooks——第 15 章〈代理間通訊（A2A）〉

### Z

- 零樣本學習（Zero-Shot Learning）——第 9 章〈學習與適應〉
- 零樣本提示（Zero-Shot Prompting）——附錄 A

## Prompt

以下為生成此索引時使用的提示（原文保留）。

> *Do you read <https://docs.google.com/document/d/1rsaK53T3Lg5KoGwvf8ukOUvbELRtH-V0LnOIFDxBryE/edit?tab=t.0> and all the related documents in the first page Please extract all the titles for each chapter. and print them. Then process the content of all the individual chapters, and create a detailed index used for publishing, a list of words or phrases ('headings') and associated pointers ('locators') to where useful material relating to that heading can be found in a document or collection of documents. I don't want a sample index. I want the full index, with 100 index terms*

## Gemini 2.5：推理步驟

### 開始建立索引

我已經開始閱讀指定的 Google 文件，現階段重點是理解使用者要求：擷取章節標題並建構詳細索引。我正仔細檢視文件，尋找章節分隔的結構線索，例如一致的格式或關鍵字，藉此引導後續索引流程。

### 定義索引擷取流程

目前我全情投入任務，已經將使用者要求拆成可執行步驟。第一步是用 Workspaces 存取主文件，再解析內容以尋找相關文件。接著同樣用 Workspaces 讀取這些文件，最終擷取每份文件的章節標題，建立完整索引。我已經規劃好細節流程。

### 優化擷取策略

使用者指示非常清楚：閱讀主文件及相關文件，並建立詳細索引。我訂立了多步驟計劃。首先用 Workspaces 存取主文件；其次，解析並找出相關文件，同樣以 Workspaces 讀取。最後，擷取所有章節標題，整理成所需索引。我優先考慮準確度，以滿足使用者對全面索引的需求。

#### 1. 確立使用者意圖

我確認使用者想要章節標題清單和完整索引。下一步就是選擇最合適的擷取與索引方式，並評估各種 API 功能，以處理不同文件結構。

#### 2. 考慮工具使用

我已透過正則表達式由主文件擷取所有章節網址，準備使用 Workspaces 讀取實際內容。取得章節文本之後，就可以展開索引。

### 存取章節內容

我成功識別主文件內的章節網址，下一步會用 Workspaces 抓取內容，為生成索引準備文本。我聚焦效率，希望盡快處理完資料。

### 擷取章節內容

我已掌握章節網址，計劃透過批次呼叫 Workspaces 取得文本，再整理成所需索引。這樣可以確保所有相關資料都被捕捉，為建立全面、準確的結果鋪路。

### 啟動網址擷取

我已設定好對 Workspaces 的呼叫，準備下載每個章節內容。目標是取得索引用的文本，之後就可以建立至少 100 個條目的索引。

### 開始章節擷取

我成功抽取全部章節網址，正逐一使用 Workspaces 取得內容。這些資料會用來生成索引，確保條目數量達到要求。我已開始提取。

### 協調章節擷取

我已發出 Workspaces 呼叫，並以批次處理避免速率限制，確保順利收集每個章節文本，為索引建立奠定基礎。

### 解析使用者需求

我確認使用者需要兩個主要成果：一份章節標題清單，和一個至少包含 100 個索引詞條的詳細索引。提取標題相對直接，而編製索引就需要更深入分析。

### 擷取標題並建索引

我已經讀取 31 份 Google 文件（包括主文件和所有章節），確認不需要額外工具。計劃先根據文件檔名整理章節標題，再仔細處理文本，建立含章節定位的索引。

### 制定擷取計劃

我的做法已更趨精準：先從 31 份文件的檔名整理章節標題，再細緻分析內容，製作超過 100 個條目的字母排序索引。計劃完成，可以開始執行。

### 具體化策略

策略已經明確。先整理章節標題，接著分析內容，建立按字母排序且附章節定位的索引。使用者所需的詳細索引——涵蓋《代理型設計模式》系列各章及附錄——已經指日可待。我會專注提供深入結果，而不是示範性樣本。

### 確定實施方式

我已澄清使用者需求：由檔名擷取章節標題，然後細緻建立超過 100 條目、帶章節定位的索引。所有準備就緒，可以開始索引和擷取流程。
