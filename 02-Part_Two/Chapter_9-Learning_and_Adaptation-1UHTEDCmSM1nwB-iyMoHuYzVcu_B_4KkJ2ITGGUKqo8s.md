# 第九章：學習（Learning）與適應（Adaptation）

學習（Learning）與適應（Adaptation）是提升人工智能代理（artificial intelligence agents）能力的關鍵。這些過程讓代理（agents）能夠超越預先設定的參數，透過經驗與環境互動自主改進。藉由學習與適應，代理可以有效應對新情況，並在無需持續人工干預下優化表現。本章深入探討支撐代理學習與適應的原理與機制。

## 整體觀點（The Big Picture）

代理會根據新的經驗與數據調整思維、行動或知識，從而達致學習與適應。這使得代理能從僅僅遵循指令逐步演化為隨時間變得更聰明的系統。

* **強化學習（Reinforcement Learning）：** 代理透過嘗試行動並根據正向成果獲得獎勵、負向成果承受懲罰，在變動情境中學習最佳行為。適用於控制機械人或玩遊戲的代理。
* **監督式學習（Supervised Learning）：** 代理透過標註範例學習，將輸入對應至期望輸出，從而支援決策制定與模式識別。適合處理電郵分類或趨勢預測的代理。
* **非監督式學習（Unsupervised Learning）：** 代理在未標註資料中發掘隱藏的關聯與模式，有助洞察、組織與建立環境的心理地圖。適合在缺乏特定指引下探索資料的代理。
* **少量示例／零示例學習（Few-Shot/Zero-Shot Learning）與大型語言模型代理（LLM-Based Agents）：** 利用大型語言模型（Large Language Models, LLMs）的代理可在極少示例或清晰指示下快速適應新任務，使其能迅速對新指令或情境作出回應。
* **線上學習（Online Learning）：** 代理持續以新資料更新知識，這對於在動態環境中實現即時反應與持續適應至關重要。對處理連續資料流的代理尤其關鍵。
* **記憶式學習（Memory-Based Learning）：** 代理回憶過往經驗，在類似情境中調整當前行動，強化情境感知與決策能力。對具備記憶召回能力的代理十分有效。

代理透過學習改變策略、理解或目標來完成適應，這對於置身不可預測、變化多端或陌生環境的代理至關重要。

**近端策略優化（Proximal Policy Optimization, PPO）** 是一種強化學習演算法，用於訓練在連續動作空間中運作的代理，例如控制機械人關節或遊戲角色。其主要目標是可靠且穩定地改善代理的決策策略（policy）。

PPO 的核心概念是對代理策略進行小幅且謹慎的更新，避免導致表現崩潰的劇烈改變。其運作方式如下：

1. 收集資料：代理以現有策略與環境互動（例如遊玩遊戲），並收集一批經驗（狀態、行動、獎勵）。
2. 評估「代理目標」（surrogate goal）：PPO 計算潛在策略更新將如何改變期望獎勵。然而，它並非單純最大化該獎勵，而是使用特製的「截斷」（clipped）目標函數。
3. 「截斷」機制：這是 PPO 穩定性的關鍵。它在當前策略周圍建立「信任區域」（trust region）或安全區，防止演算法做出與現有策略差異過大的更新。這種截斷猶如安全煞車，確保代理不會跨出巨大且風險極高的一步而破壞既有學習成果。

總而言之，PPO 在提升表現與維持接近已知可行策略之間取得平衡，防止訓練期間發生災難性失敗，並帶來更穩定的學習。

**直接偏好優化（Direct Preference Optimization, DPO）** 是專為讓大型語言模型（Large Language Models, LLMs）符合人類偏好而設計的較新方法，為此任務提供比 PPO 更簡潔直接的替代方案。

要理解 DPO，先了解傳統的以 PPO 為基礎的對齊（alignment）方法會有所幫助：

* **PPO 方法（兩步驟流程）：**
  1. 訓練獎勵模型：首先收集人類回饋資料，讓人們評分或比較不同的 LLM 回應（例如「回應 A 比回應 B 更好」）。這些資料用於訓練另一個人工智能模型（AI model），稱為獎勵模型（reward model），其任務是預測人類會給任何新回應的分數。
  2. 以 PPO 微調：接著使用 PPO 微調 LLM。LLM 的目標是產生能從獎勵模型取得最高可能分數的回應。獎勵模型在這場訓練遊戲中扮演「評審」。

這個兩步驟流程可能複雜且不穩定。例如，LLM 可能找出漏洞，學會「破解」獎勵模型，讓劣質回應獲得高分。

* **DPO 方法（直接流程）：** DPO 完全略過獎勵模型。它不再將人類偏好轉換成獎勵分數再進行優化，而是直接利用偏好資料更新 LLM 的策略。
* DPO 透過數學關係直接連結偏好資料與最適策略。它本質上教導模型：「提高生成與*偏好*回應相似的機率，降低生成類似*不受偏好*回應的機率。」

總之，DPO 透過直接在偏好資料上優化語言模型，簡化了對齊過程，避免訓練與使用獨立獎勵模型的複雜與潛在不穩定，讓對齊過程更高效且更具韌性。

## 實務應用與使用案例（Practical Applications & Use Cases）

適應性代理（adaptive agents）透過根據經驗數據進行反覆更新，在變動環境中展現更佳表現。

* **個人化助理代理（Personalized assistant agents）** 透過對個別使用者行為進行長期分析，精煉互動協議，確保回應生成高度優化。
* **交易機械人代理（Trading bot agents）** 依據高解析度、即時市場數據動態調整模型參數，以最大化財務回報並降低風險因素。
* **應用程式代理（Application agents）** 根據觀察到的使用者行為動態調整介面與功能，從而提高使用者參與度與系統直覺性。
* **機械人與自動駕駛車輛代理（Robotic and autonomous vehicle agents）** 整合感測器數據與歷史行動分析，增強導航與反應能力，確保在各種環境條件下安全且高效地運作。
* **防詐騙代理（Fraud detection agents）** 透過新識別的詐騙模式精進預測模型，加強系統安全並減少財務損失。
* **推薦代理（Recommendation agents）** 應用使用者偏好學習演算法，提升內容選擇的準確度，提供高度個人化且具情境相關性的推薦。
* **遊戲人工智能代理（Game AI agents）** 動態調整策略演算法以提升玩家參與度，從而增加遊戲複雜度與挑戰性。
* **知識庫學習代理（Knowledge Base Learning Agents）：** 代理可運用檢索增強生成（Retrieval Augmented Generation, RAG）維持動態的問題描述與已驗證解決方案知識庫（詳見第十四章）。藉由儲存成功策略與遭遇挑戰，代理能在決策時參照這些資料，更有效地將既有成功模式套用至新情境或避免已知陷阱。

## 案例研究：自我改進編碼代理（Self-Improving Coding Agent, SICA）

由 Maxime Robeyns、Laurence Aitchison 與 Martin Szummer 開發的自我改進編碼代理（Self-Improving Coding Agent, SICA）代表了代理學習的進展，展現代理修改自身原始碼的能力。這與傳統由一個代理訓練另一個代理的方法不同；SICA 同時扮演修改者與被修改者，透過反覆精煉自身程式碼基礎來提升在各種編碼挑戰中的表現。

SICA 的自我改進以迭代循環運作（見圖一）。首先，SICA 檢視過往版本及其在基準測試上的表現。它會根據考量成功率、時間與計算成本的加權公式，挑選分數最高的版本。該版本接著承擔下一輪自我修改。代理分析檔案庫以識別潛在改進，並直接修改其程式碼基礎。修改後的代理會再接受基準測試，結果記錄在檔案庫中。這個流程不斷重複，使其能直接從過去表現中學習。這種自我改進機制使 SICA 無需傳統訓練典範即可演化其能力。

![SICA's self-improvement, learning and adapting based on its past versions](../assets/SICAs_self_improvement_learning_and_adapting_based_on_its_past_versions.png)

圖一：SICA 透過過往版本的表現進行自我改進、學習與適應。

SICA 的自我改進帶來在程式碼編輯與導覽上的重大進展。起初，SICA 以基本的檔案覆寫方式進行程式碼變更；其後，它開發出能進行更智慧且具情境判斷的「智慧編輯器（Smart Editor）」。此工具進一步演化為整合差異比較的「差異增強智慧編輯器（Diff-Enhanced Smart Editor）」，以便進行精準修改與基於模式的編輯，並搭配「快速覆寫工具（Quick Overwrite Tool）」降低處理需求。

SICA 亦實作了「最小差異輸出優化（Minimal Diff Output Optimization）」與「情境感知差異最小化（Context-Sensitive Diff Minimization）」，利用抽象語法樹（Abstract Syntax Tree, AST）剖析提高效率。此外，它增設了「SmartEditor 輸入正規化器（SmartEditor Input Normalizer）」。在導覽方面，SICA 獨立建構「AST 符號定位器（AST Symbol Locator）」，利用程式碼結構圖（AST）識別程式碼庫中的定義。其後，它還……

在核心智慧層面，代理框架支援整合來自不同供應商的大型語言模型（Large Language Models, LLMs），讓研究人員能試驗不同模型以尋找最適合特定任務的選擇。最後，非同步監督者（asynchronous overseer）是至關重要的元件，這是一個與主代理同時運行的大型語言模型。該監督者會定期評估代理的行為是否出現病態偏差或停滯，必要時可介入發送通知甚至取消代理的執行。它會接收系統狀態的詳細文字表示，包括呼叫圖（callgraph）與 LLM 訊息、工具呼叫與回應的事件流，使其能偵測低效率模式或重複工作。

在最初的 SICA 實作中，一項顯著挑戰是如何提示以大型語言模型為基礎的代理在每次元改進（meta-improvement）迭代中自主提出嶄新、創意、可行且具吸引力的修改。這一限制——特別是在促進大型語言模型代理展現開放式學習與真正創造力方面——仍是當前研究的核心議題。

## AlphaEvolve 與 OpenEvolve

**AlphaEvolve** 是 Google 開發的人工智能代理（AI agent），旨在發掘與優化演算法。它結合大型語言模型（Large Language Models, LLMs），尤其是 Gemini 模型（Flash 與 Pro）、自動化評估系統與演化演算法框架（evolutionary algorithm framework）。此系統旨在推進理論數學與實務計算應用。

AlphaEvolve 採用 Gemini 模型組合。Flash 用於生成各式初始演算法提案，而 Pro 則提供更深入的分析與精煉。所提出的演算法會自動依預先定義的標準評估與評分。這些評估提供回饋，用於反覆改進解決方案，進而產生優化且創新的演算法。

在實務計算方面，AlphaEvolve 已部署於 Google 的基礎設施。它在資料中心排程上展現改進，達致全球計算資源使用量降低 0.7%。它亦透過為即將推出的張量處理單元（Tensor Processing Units, TPUs）提供 Verilog 程式碼優化建議，促進硬體設計。此外，AlphaEvolve 提升了人工智能（AI）效能，包括讓 Gemini 架構的核心內核（core kernel）加速 23%，以及將 FlashAttention 的低階 GPU 指令優化至多 32.5%。

在基礎研究領域，AlphaEvolve 協助發現新的矩陣乘法演算法，包括針對 4×4 複數矩陣僅需 48 次純量乘法的方法，超越以往已知解法。更廣泛的數學研究中，它在 75% 的案例裡重現既有最先進解法，並在 20% 的案例中實現突破，例子包括接觸數問題（kissing number problem）的進展。

**OpenEvolve** 是一款利用大型語言模型（Large Language Models, LLMs）的演化式編碼代理（evolutionary coding agent）（見圖三），可透過迭代方式優化程式碼。它協調由大型語言模型驅動的程式碼生成、評估與選擇流程，持續提升涵蓋廣泛任務的程式。OpenEvolve 的一大特色是能夠演化整個程式碼檔案，而非僅限單一函式。該代理追求多功能性，支援多種程式語言，並與任何大型語言模型的 OpenAI 相容應用程式介面（OpenAI-compatible APIs）兼容。此外，它整合多目標優化（multi-objective optimization）、允許彈性提示工程（prompt engineering），並能進行分散式評估以高效處理複雜編碼挑戰。

![OpenEvolve Architecture](../assets/OpenEvolve_Architecture.png)

圖三：OpenEvolve 的內部架構由控制器（controller）管理。此控制器協調多個關鍵組件：程式採樣器（program sampler）、程式資料庫（Program Database）、評估者集區（Evaluator Pool）與大型語言模型組合（LLM Ensembles）。其主要功能是促進它們的學習與適應過程，以提升程式碼品質。

以下程式碼片段使用 OpenEvolve 函式庫對程式進行演化式優化。它以初始程式路徑、評估檔案與設定檔路徑初始化 OpenEvolve 系統。`evolve.run(iterations=1000)` 會啟動演化流程，執行 1000 次迭代以尋找更佳版本的程式。最後，程式會印出在演化過程中找到的最佳程式指標，並格式化至小數點後四位。

```python
from openevolve import OpenEvolve


# Initialize the system
evolve = OpenEvolve(
    initial_program_path="path/to/initial_program.py",
    evaluation_file="path/to/evaluator.py",
    config_path="path/to/config.yaml",
)

# Run the evolution
best_program = await evolve.run(iterations=1000)

print("Best program metrics:")
for name, value in best_program.metrics.items():
    print(f"  {name}: {value:.4f}")
```

## 重點一覽(At a Glance)

**內容（What）：** 人工智能代理常在動態且不可預測的環境中運作，預先編寫的邏輯往往不足。一旦遭遇初始設計時未預期的新情境，其效能可能下降。若沒有從經驗中學習的能力，代理無法隨時間優化策略或個人化互動。這種僵化限制其效能，也使其無法在複雜真實情境中達成真正的自主性。

**原因（Why）：** 標準化的解決方案是整合學習與適應機制，將靜態代理轉化為動態且會演化的系統。這讓代理能根據新資料與互動自主精煉知識與行為。代理系統可使用多種方法，從強化學習到如自我改進編碼代理（Self-Improving Coding Agent, SICA）所展示的進階技巧。Google 的 AlphaEvolve 等先進系統結合大型語言模型與演化演算法，能自行發現全新的高效率解決方案來處理複雜問題。透過持續學習，代理得以掌握新任務、提升表現，並在無需不斷人工重新編程的情況下適應變化。

**經驗法則（Rule of thumb）：** 當需要打造能在動態、不確定或持續演化的環境中運作的代理時，就應採用此模式。這對需要個人化、持續提升效能與自主處理新情境的應用至關重要。

**視覺摘要（Visual summary）：**

![Learning and Adapting Pattern](../assets/Learning_and_Adapting_Pattern.png)

圖四：學習與適應模式。

## 重要重點（Key Takeaways）

* 學習與適應旨在讓代理透過經驗改善能力並處理新情境。
* 「適應」是代理因學習而產生的可見行為或知識變化。
* 自我改進編碼代理（Self-Improving Coding Agent, SICA）透過根據過往表現修改程式碼來自我改進，催生出智慧編輯器（Smart Editor）與 AST 符號定位器（AST Symbol Locator）等工具。
* 將專門的「子代理」（sub-agents）與「監督者」（overseer）結合，有助這些自我改進系統處理大型任務並保持方向正確。

* 大型語言模型（Large Language Models, LLMs）的「脈絡視窗」（context window）配置（包括系統提示、核心提示與助理訊息）對代理的效率極為重要。
* 此模式對需要在持續變動、不確定或需要個人化處理的環境中運作的代理至關重要。
* 建置能學習的代理通常意味著必須整合機器學習工具並管理資料流。
* 配備基本編碼工具的代理系統可以自動編輯自身，從而提升在基準任務上的表現。
* AlphaEvolve 是 Google 的人工智能代理，利用大型語言模型與演化框架自主探索與優化演算法，顯著提升基礎研究與實務計算應用。

## 結論（Conclusion）

本章檢視學習與適應在人工智能中的關鍵角色。人工智能代理透過持續獲取資料與累積經驗來提升效能。自我改進編碼代理（Self-Improving Coding Agent, SICA）即為典範，能透過程式碼修改自主強化能力。

我們回顧了代理型人工智能的基本組成，包括架構、應用、規劃、多代理協作、記憶管理，以及學習與適應。學習原則對多代理系統的協調改進尤其重要。為達成此目標，調校資料必須準確反映完整的互動軌跡，捕捉每個參與代理的輸入與輸出。

這些元素促成如 Google 的 AlphaEvolve 等重大進展。此人工智能系統（AI system）結合大型語言模型（Large Language Models, LLMs）、自動化評估與演化式方法（evolutionary approach），自主發掘並精煉演算法，推動科學研究與計算技術的進步。這類模式可互相組合，構建精密的人工智能系統。AlphaEvolve 等成果顯示，人工智能代理（AI agents）實現自主演算法發現與優化是可行的。

## References

1. Sutton, R. S., & Barto, A. G. (2018). *Reinforcement Learning: An Introduction*. MIT Press.
2. Goodfellow, I., Bengio, Y., & Courville, A. (2016). *Deep Learning*. MIT Press.
3. Mitchell, T. M. (1997). *Machine Learning*. McGraw-Hill.
4. **Proximal Policy Optimization Algorithms** by John Schulman, Filip Wolski, Prafulla Dhariwal, Alec Radford, and Oleg Klimov. You can find it on arXiv: [https://arxiv.org/abs/1707.06347](https://arxiv.org/abs/1707.06347)
5. Robeyns, M., Aitchison, L., & Szummer, M. (2025). *A Self-Improving Coding Agent*. arXiv:2504.15228v2. [https://arxiv.org/pdf/2504.15228](https://arxiv.org/pdf/2504.15228)  [https://github.com/MaximeRobeyns/self_improving_coding_agent](https://github.com/MaximeRobeyns/self_improving_coding_agent)
6. AlphaEvolve blog, [https://deepmind.google/discover/blog/alphaevolve-a-gemini-powered-coding-agent-for-designing-advanced-algorithms/](https://deepmind.google/discover/blog/alphaevolve-a-gemini-powered-coding-agent-for-designing-advanced-algorithms/)
7. OpenEvolve, [https://github.com/codelion/openevolve](https://github.com/codelion/openevolve)
