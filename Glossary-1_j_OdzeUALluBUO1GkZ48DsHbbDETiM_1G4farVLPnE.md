# 詞彙表（Glossary）

## 基礎概念

**提示（Prompt）：** 提示是使用者提供給人工智能（AI）模型的輸入內容，通常為問題、指令或陳述，用以觸發模型回應。提示的品質與結構會深刻影響輸出，因此提示工程（Prompt Engineering）是有效運用 AI 的核心技能。

**脈絡視窗（Context Window）：** 脈絡視窗指模型在一次互動中可處理的最大標記數量，涵蓋輸入與輸出。這項固定限制意味著超出視窗的資料會被忽略；視窗越大，越能支援複雜對話與長文件分析。

**脈絡內學習（In-Context Learning）：** 脈絡內學習指模型可根據提示中提供的示例，即時掌握新任務，而無須重新訓練。此特性使單一通用模型能即場適應大量特定任務。

**零樣本、單樣本與少樣本提示（Zero-Shot, One-Shot & Few-Shot Prompting）：** 這些提示技巧在輸入中提供零個、一個或少量示例，引導模型作答。一般而言，示例越多，模型越能理解使用者意圖，在指定任務上的表現亦越準確。

**多模態（Multimodality）：** 多模態表示 AI 能理解並處理多種數據類型，例如文字、圖像與音訊。此能力使互動更全面，亦更貼近人類，例如能描述圖片或回應語音提問。

**錨定（Grounding）：** 錨定是將模型輸出連結至可驗證的現實資訊來源，以確保事實準確並減少幻覺（Hallucination）。常見作法包括運用檢索增強生成（RAG），提升 AI 系統的可靠性。

## 核心 AI 模型架構

**Transformer：** Transformer 是現代大型語言模型的基礎神經網絡架構，創新之處在自注意力（Self-Attention）機制，可高效處理長文本序列並掌握詞語之間的複雜關係。

**循環神經網絡（Recurrent Neural Network，RNN）：** RNN 是 Transformer 出現前的主流架構，以序列方式處理資訊，透過迴圈維持「記憶」，因此適合文字與語音等任務。

**專家混合（Mixture of Experts，MoE）：** MoE 架構透過「路由器」網絡動態選擇少量「專家」子網絡處理輸入。即使模型擁有龐大參數量，也能藉此控制計算成本。

**擴散模型（Diffusion Models）：** 擴散模型擅長生成高品質圖像。模型先向數據加入噪音，再學習逐步還原，最終可由隨機起點產生全新內容。

**Mamba：** Mamba 是近年提出的架構，使用選擇性狀態空間模型（Selective State Space Model，SSM）高效處理序列，特別適合極長脈絡。透過選擇性機制聚焦關鍵資訊並過濾噪音，被視為 Transformer 的潛在替代方案。

## 大型語言模型（LLM）開發生命週期

強大的語言模型通常歷經多個階段。首先為預訓練（Pre-training），利用大量網絡文本建立基礎模型，使其習得語言、推理與世界知識。接續進入微調（Fine-tuning）階段，在較小且任務導向的數據集上進一步訓練，讓模型適應特定用途。最後透過對齊（Alignment），調整模型行為以確保輸出有用、無害且符合人類價值。

**預訓練技巧（Pre-training Techniques）：** 預訓練是模型吸收廣泛知識的起點。常見目標包括因果語言建模（Causal Language Modeling，CLM），即預測下一個詞；以及遮罩語言建模（Masked Language Modeling，MLM），要求模型填補刻意隱藏的詞語。其他做法尚有去噪目標（Denoising Objectives），訓練模型還原受破壞的輸入；對比式學習（Contrastive Learning），辨識資料之間的相似與差異；以及下一句預測（Next Sentence Prediction，NSP），判斷兩句是否合理銜接。

**微調技巧（Fine-tuning Techniques）：** 微調透過較小且專門的數據集，使通用預訓練模型適應特定任務。最常見方法為監督式微調（Supervised Fine-Tuning，SFT），以標註好的輸入輸出示例訓練模型。指令微調（Instruction Tuning）是熱門變體，專注提升模型遵循使用者指令的能力。為提升效率，常採參數高效微調（Parameter-Efficient Fine-Tuning，PEFT）技術，例如僅更新少量參數的 LoRA（Low-Rank Adaptation）及記憶體優化版本 QLoRA。另一項關鍵技巧是檢索增強生成（RAG），在微調或推理期間連結外部知識來源，以擴充模型能力。

**對齊與安全技巧（Alignment & Safety Techniques）：** 對齊旨在確保模型行為符合人類價值與期望，做到既有用又無害。最著名的方法為源自人類回饋的強化學習（Reinforcement Learning from Human Feedback，RLHF），透過訓練反映人類偏好的「獎勵模型」指導 AI 學習，常與近端策略優化（Proximal Policy Optimization，PPO）等演算法搭配。較簡化的替代方案包括直接偏好優化（Direct Preference Optimization，DPO），無需額外獎勵模型；以及 Kahneman-Tversky Optimization（KTO），進一步降低資料收集成本。部署階段通常會加上護欄（Guardrails）作為最後安全層，及時過濾輸出並阻擋危險行為。

## 提升 AI 代理能力

AI 代理能夠感知環境並自主行動以達成目標，運用強大的推理框架可顯著提升其效能。

**思維鏈（Chain of Thought，CoT）：** 此提示技巧鼓勵模型先逐步說明推理，再給出最終答案，猶如「邊思考邊說」，對處理複雜推理任務更為準確。

**思維樹（Tree of Thoughts，ToT）：** 思維樹是進階推理框架，代理會沿著樹狀結構探索多條推理路徑，自我評估不同想法並選擇最有前景的一條，從而提升解題能力。

**ReAct（Reason and Act）：** ReAct 將推理與行動結合。代理先「思考」下一步，再調用工具執行「行動」，並依據觀察結果調整後續決策，特別適合複雜任務。

**規劃（Planning）：** 規劃能力使代理能把高層目標拆解為一系列較小且可管理的子任務，並按順序制定執行計畫，以處理多步驟與複雜任務。

**深度研究（Deep Research）：** 深度研究指代理可自主深入探索主題，反覆搜尋資訊、綜合理解並挖掘新問題，累積遠超單次查詢的全面知識。

**評論模型（Critique Model）：** 評論模型專門用於審閱、評估並回饋另一個 AI 模型的輸出，如同自動化評論員，能識別錯誤、改善推理，確保成果符合預期品質。
