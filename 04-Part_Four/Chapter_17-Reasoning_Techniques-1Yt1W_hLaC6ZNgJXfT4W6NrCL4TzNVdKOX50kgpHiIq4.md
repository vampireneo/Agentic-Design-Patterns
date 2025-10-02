# 第17章：推理技巧（Reasoning Techniques）

本章深入探討智能代理的進階推理方法，焦點在於多步驟邏輯推論與解題能力。這些技巧超越簡單的順序操作，令代理的內部思考過程變得透明，讓代理可以拆解問題、考慮中間步驟，並得出更穩健及準確的結論。這類進階方法的核心原則，是在推論期間分配更多運算資源，亦即給予代理或底層大型語言模型（LLM）更多處理查詢與產出回應的時間或步驟。代理不再只是一遍過，而是可以進行反覆修訂、探索多條解決路徑，或使用外部工具。這段額外的推論時間，往往能大幅提升面對複雜問題時的準確度、連貫性與穩健性。

## 實際應用與使用情境（Practical Applications & Use Cases）

實務應用包括：

* **複雜問題解答（Complex Question Answering）：** 協助解決需要多重跳躍推論的查詢，整合不同來源的數據並執行邏輯推斷，可能需要檢視多條推理路徑，並利用延長推論時間來綜合資訊。
* **數學問題求解（Mathematical Problem Solving）：** 讓代理把數學問題拆成較小且可解的部分，展示逐步過程，並利用程式碼執行精確運算；較長的推論時間可生成更複雜的程式碼並加以驗證。
* **程式碼除錯與產生（Code Debugging and Generation）：** 支援代理解釋其產生或修正程式碼的理由，按步驟指出潛在問題，並根據測試結果反覆修訂（Self-Correction），藉由延長推論時間完成更徹底的除錯循環。
* **策略規劃（Strategic Planning）：** 協助代理在多個選項、結果與前置條件之間進行推理，並基於即時回饋（ReAct）調整計劃；較長的思考時間可帶來更有效及更可靠的計劃。
* **醫療診斷（Medical Diagnosis）：** 協助代理有系統地評估症狀、檢查結果與病歷，逐步說明推理過程，並視需要運用外部工具檢索資料（ReAct）。增加推論時間有助於更全面的鑑別診斷。
* **法律分析（Legal Analysis）：** 協助分析法律文件與先例，以建構論點或提供建議，詳細交代所採用的邏輯步驟，並透過自我修正維持一致性。延長推論時間可支持更深入的法律研究與論證建構。

## 推理技巧（Reasoning Techniques）

首先，我們來探討能提升人工智能模型解題能力的核心推理技巧。

**鏈式思考（Chain-of-Thought，CoT）** 提示透過模仿逐步思考過程（見圖1），顯著提升大型語言模型的複雜推理能力。CoT 提示不是直接給答案，而是引導模型產出一連串中間推理步驟。清晰的拆解讓模型可以把複雜問題分成較小且可管理的子問題，對需要多步推理的任務（例如算術、常識推理、符號操作）特別有效。CoT 的主要優點，是把困難的單步問題轉化為一系列較簡單的步驟，增加模型推理過程的透明度。這種做法不但提升準確度，還可提供模型決策的洞察，協助除錯與理解。CoT 可以透過多種策略實現，包括提供展示逐步推理的少量示例，或單純指示模型「逐步思考」。其成效來自於引導模型的內部處理更審慎、更具邏輯性。因此，鏈式思考已成為當代大型語言模型實現進階推理能力的基石。這種透明度與拆解複雜問題的能力，對自主代理尤其重要，因為它能讓代理在複雜環境中採取更可靠、可稽核的行動。

![COT: Chain of Thought](../assets/COT_Chain_of_Thought.png)

圖1：展示 CoT 提示與代理產生的詳細逐步回應。

我們來看看例子。流程由一組指示開始，告訴人工智能如何思考，設定其角色與清晰的五步流程，這就是啟動結構化思維的提示。

接着，範例展示 CoT 運作過程。標示為「代理的思考過程」的部分，是模型執行指示步驟的內部獨白，也就是實際的「思路鏈」。最後，「代理的最終答案」是經過仔細、逐步推理後產生的完整輸出。

```markdown
You are an Information Retrieval Agent. Your goal is to answer the user's question comprehensively and accurately by thinking step-by-step.

Here's the process you must follow:
1.  **Analyze the Query:** Understand the core subject and specific requirements of the user's question. Identify key entities, keywords, and the type of information being sought.
2.  **Formulate Search Queries (for Knowledge Base):** Based on your analysis, generate a list of precise search queries that you would use to retrieve relevant information from a knowledge base or external tools.
3.  **Simulate Information Retrieval (Self-Correction/Reasoning):** For each search query, mentally consider what kind of information you expect to find. If you were to retrieve the content, what would be the most relevant snippets? Think about potential ambiguities or missing pieces.
4.  **Synthesize Information:** Based on the simulated retrieval and your understanding of the user's original query, synthesize the gathered information into a coherent and complete answer. Ensure all aspects of the query are addressed.
5.  **Review and Refine:** Before finalizing, critically evaluate your answer. Is it accurate? Is it comprehensive? Is it easy to understand? Is it concise? If not, identify what needs to be improved and how.

**User Query:** "Explain the main differences between classical computers and quantum computers, and briefly describe a potential application of quantum computing."

**Agent's Thought Process (Internal CoT Output - encouraged by the prompt):**

**Thought 1 (Analyze Query):**
The user wants two things:
    1.  Main differences between classical and quantum computers.
    2.  One potential application of quantum computing. Keywords: classical computers, quantum computers, differences, potential application.

**Thought 2 (Formulate Search Queries):**
    - "differences classical vs quantum computers"
    - "how quantum computers work"
    - "how classical computers work"
    - "applications of quantum computing"

**Thought 3 (Simulate Information Retrieval & Identify Key Concepts):**
    - From "differences": Expect concepts like bits vs. qubits, superposition, entanglement, probabilistic nature, computational power for specific problems.
    - From "how they work": Classical uses transistors/binary. Quantum uses quantum mechanics.
    - From "applications": Expect drug discovery, material science, cryptography, optimization.

**Thought 4 (Synthesize Information):**
Classical computers use bits (0 or 1), processing information sequentially. Quantum computers use qubits, which can be 0, 1, or both simultaneously (superposition), and can be entangled, allowing for vastly different computational approaches. This enables them to solve certain problems (like factoring large numbers or simulating complex molecules) far more efficiently than classical computers. A potential application is in drug discovery, where quantum computers could simulate molecular interactions with unprecedented accuracy, accelerating the development of new medicines.

**Thought 5 (Review and Refine):**
    - Differences are covered (bits vs qubits, superposition, entanglement, processing).
    - Application is given (drug discovery). - Is it brief? Yes. - Is it clear? Yes. - Looks good.

**Agent's Final Answer (Output to User):**
Classical computers process information using bits, which can be either a 0 or a 1 at any given time, performing operations sequentially. In contrast, quantum computers utilize qubits, which can exist in multiple states simultaneously through superposition and can be linked through entanglement, allowing for exponentially more complex computations. This fundamental difference enables quantum machines to tackle certain problems, such as simulating molecular structures or breaking complex encryption, that are intractable for even the most powerful classical supercomputers. A significant potential application of quantum computing lies in drug discovery, where its ability to precisely model molecular behavior could revolutionize the development of new pharmaceuticals.
```

...

這個流程的核心在於引導代理產生多個想法、識別改進之處，並以清晰的系統檢查清單形式呈現：語氣、準確度、具體建議、代替字詞、最佳實務、格式、避免重複字詞與表情符號。流程最後輸出「最終修訂內容」，即整合自我檢視調整後的精緻版本。

```markdown
You are a highly critical and detail-oriented Self-Correction Agent. Your task is to review a previously generated piece of content against its original requirements and identify areas for improvement. Your goal is to refine the content to be more accurate, comprehensive, engaging, and aligned with the prompt.

Here's the process you must follow for self-correction:

1.  **Understand Original Requirements:** Review the initial prompt/requirements that led to the content's creation. What was the *original intent*? What were the key constraints or goals?
2.  **Analyze Current Content:** Read the provided content carefully.
3.  **Identify Discrepancies/Weaknesses:** Compare the current content against the original requirements. Look for:
    * **Accuracy Issues:** Are there any factual errors or misleading statements?
    * **Completeness Gaps:** Does it fully address all aspects of the original prompt? Is anything missing?
    * **Clarity & Coherence:** Is the language clear, concise, and easy to understand? Does it flow logically?
    * **Tone & Style:** Does it match the desired tone and style (e.g., professional, engaging, concise)?
    * **Engagement:** Is it captivating? Does it hold the reader's attention?
    * **Redundancy/Verbosity:** Can any parts be condensed or removed without losing meaning?
4.  **Propose Specific Improvements:** For each identified weakness, suggest concrete and actionable changes. Do not just state the problem; propose a solution.
5.  **Generate Revised Content:** Based on your proposed improvements, rewrite the original content to incorporate all the necessary changes. Ensure the revised content is polished and ready for final use.

**Original Prompt/Requirements:** "Write a short, engaging social media post (max 150 characters) announcing a new eco-friendly product line: 'GreenTech Gadgets'."

**Initial Draft (Content to be corrected):** "We have new products. They are green and techy. Buy GreenTech Gadgets now!"

**Self-Correction Agent's Thought Process (Internal CoT Output - encouraged by the prompt):**

**Thought 1 (Understand Original Requirements):**
    - Short, engaging social media post.
    - Max 150 characters.
    - Announce "GreenTech Gadgets" - a new eco-friendly product line.

**Thought 2 (Analyze Current Content):**
"We have new products. They are green and techy. Buy GreenTech Gadgets now!" (64 characters)

**Thought 3 (Identify Discrepancies/Weaknesses):** - **Engaging:** Not very engaging. Sounds generic. - **Eco-friendly:** Implied by "green," but not explicitly highlighted as a benefit. - **Call to Action:** "Buy now!" is okay, but could be more compelling. - **Conciseness:** It's short, but could use stronger verbs/phrasing. - **Impact:** Lacks excitement for a new product launch.

**Thought 4 (Propose Specific Improvements):** - Make it more dynamic and exciting. - Emphasize "eco-friendly" and "innovation." - Use stronger verbs and more active voice. - Add a clear, engaging call to action. - Ensure it stays under 150 characters. **Thought 5 (Generate Revised Content):** Original: "We have new products. They are green and techy. Buy GreenTech Gadgets now!" Revised idea 1: "🌱 Discover GreenTech Gadgets! Our new eco-friendly line blends innovation with sustainability. Go green, go smart! Shop now! #EcoFriendly #GreenTech" (148 characters) This revision directly addresses eco-friendliness, uses emojis for engagement, adds a stronger call to action, and includes relevant hashtags while staying within the character limit. **Self-Correction Agent's Final Revised Content (Output to User):** 🌱 Discover GreenTech Gadgets! Our new eco-friendly line blends innovation with sustainability. Go green, go smart! Shop now! #EcoFriendly #GreenTech
```

從本質上說，這種技巧把品質控制直接整合進代理的內容產生流程，能輸出更精緻、準確且優質的結果，以更有效地滿足複雜的使用者需求。

**程式輔助語言模型（Program-Aided Language Models，PALMs）** 將大型語言模型與符號推理能力結合。這種整合讓模型可以在解題過程中產生並執行程式碼（例如 Python）。PALM 會把複雜計算、邏輯操作與資料處理交給具決定性的程式環境處理。這個方法善用傳統編程在面對大型語言模型可能出現準確度或一致性限制時的優勢。當碰到符號挑戰時，模型可以產生程式碼、執行之，並把結果轉換成自然語言。這種混合方法結合模型的理解與生成能力，以及精準運算，使模型能以更高可靠度與準確度處理更廣泛的複雜問題。對代理而言，這非常重要，因為可以在理解與生成的基礎上，透過精準運算做出更準確可靠的行動。例子之一，是在 Google 的 Agentic Development Kit（ADK）中使用外部工具來生成程式碼。

```python
from google.adk.tools import agent_tool
from google.adk.agents import Agent
from google.adk.tools import google_search
from google.adk.code_executors import BuiltInCodeExecutor


search_agent = Agent(
    model="gemini-2.0-flash",
    name="SearchAgent",
    instruction="""
    You're a specialist in Google Search
    """,
    tools=[google_search],
)

coding_agent = Agent(
    model="gemini-2.0-flash",
    name="CodeAgent",
    instruction="""
    You're a specialist in Code Execution
    """,
    code_executor=BuiltInCodeExecutor(),
)

root_agent = Agent(
    name="RootAgent",
    model="gemini-2.0-flash",
    description="Root Agent",
    tools=[
        agent_tool.AgentTool(agent=search_agent),
        agent_tool.AgentTool(agent=coding_agent),
    ],
)
```

**可驗證獎勵強化學習（Reinforcement Learning with Verifiable Rewards，RLVR）**：雖然鏈式思考（CoT）提示非常有效，但仍屬於較基本的推理方式，它只產生單一、預定的思路，未能根據問題複雜度調整。為了突破限制，出現了一批專門的「推理模型」，會在回答前投入可變長度的「思考」時間。這段思考過程可以形成更長、更具動態的鏈式思考，長度甚至可達數千個權杖。延伸的推理讓模型能進行自我修正與回溯，對困難問題投入更多精力。促成這些模型的關鍵創新，是名為可驗證獎勵強化學習的訓練策略。透過讓模型在已有正確答案（例如數學或程式）的問題上反覆嘗試，它會學習產出有效的長篇推理，毋須人類直接監督。最終，這些推理模型不只是給出答案，更會生成展現高階能力（如規劃、監控、評估）的「推理軌跡」。這種增強的推理與策劃能力，是自主人工智能代理得以拆解並獨立解決複雜任務的根基。

**ReAct（Reasoning and Acting）**（見圖3，其中 KB 代表知識庫 Knowledge Base）是一種把鏈式思考提示與代理使用工具互動能力整合的範式。不同於只輸出最終答案的生成模型，ReAct 代理會先推敲應採取的行動。推理階段涉及與 CoT 類似的內部規劃，代理會決定下一步、考慮可用工具並預測結果。然後代理會採取行動，例如查詢資料庫、執行計算或呼叫 API。

![REACT: Reasoning and Act](../assets/REACT_Reasoning_and_Act.png)

圖3：Reasoning and Act

ReAct 以交錯方式運作：代理執行行動、觀察結果，並把觀察納入後續推理。這個「思考—行動—觀察—思考…」的循環，讓代理可以動態調整計劃、修正錯誤，並在需要多次互動的情境中達成目標。相較於線性的鏈式思考，這種方法更穩健、更靈活，因為代理會回應即時回饋。透過結合語言模型的理解與生成能力，以及使用工具的能力，ReAct 讓代理可以處理同時需要推理與實際執行的複雜任務。這對代理十分關鍵，因為它不只讓代理思考，更能實際行動並與動態環境互動。

**辯論鏈（Chain of Debates，CoD）** 是微軟提出的正式人工智能框架，由多個多樣化模型合作與辯論，以超越單一人工智能的「思路鏈」。這個系統好比人工智能會議，不同模型提出初步想法、批判彼此推理並交換反駁。主要目標是透過集體智慧提升準確度、降低偏差並改善最終答案品質。其運作類似人工智能版的同儕審查，建立透明且可信的推理紀錄，象徵從單一代理回答，走向協作式代理團隊尋找更穩健且經驗證的解決方案。

**辯論圖（Graph of Debates，GoD）** 是進階的代理框架，把討論重新想像成動態且非線性的網絡，而非簡單的鏈條。在此模型中，論點是個別節點，透過邊線表示「支持」或「反駁」等關係，反映真實辯論中的多線並行特性。這種結構讓新的探究路徑可以動態分支、獨立演化甚至重新匯合。結論不是在序列末端出現，而是透過辨識整個圖中最穩固、最受支持的論點群落而得。所謂「受支持」，指的是確立且可驗證的知識，包括被視為真實的資訊、透過搜尋驗證的事實證據，或多個模型在辯論中達成的共識。這種方法提供更全面、更貼近現實的複雜協作推理模型。

**多代理系統搜尋（Multi-Agent System Search，MASS）**（屬可選的進階主題）深入分析多代理系統設計，指出效能取決於個別代理的提示品質與決定其互動的拓撲結構。設計這類系統極為複雜，因為涉及龐大且錯綜的搜尋空間。為了解決挑戰，MASS 提供嶄新的框架，自動化並優化多代理系統的設計。

MASS 採用多階段優化策略，在提示與拓撲優化之間交錯操作，以系統性地導航複雜設計空間（見圖4）。

**1. 模組層級提示優化（Block-Level Prompt Optimization）：** 先在局部層面為個別代理類型或「模組」優化提示，確保每個元件在整合前都能有效履行角色。這一步極為重要，因為後續的拓撲優化要建立在表現良好的代理之上，避免因配置不佳而連鎖拖累。例如在 HotpotQA 資料集上進行優化時，「辯論者」代理的提示被創意地設定為「大型媒體的專業事實查證員」，其任務是仔細檢視其他代理提出的答案、與提供的上下文比對並指出不一致或缺乏支持的地方。這個在模組層級發現的角色扮演提示，能讓辯論者在進入大型工作流程前已具備極高效能。

**2. 工作流程拓撲優化（Workflow Topology Optimization）：** 在局部優化後，MASS 會在可自訂的設計空間中挑選並排列不同的代理互動，以優化工作流程拓撲。為了讓搜尋更有效率，MASS 使用影響力加權的方法，計算每種拓撲相對於基線代理的「增量影響」，並用這些分數指引搜尋朝更有前景的組合前進。例如在優化 MBPP 程式任務時，拓撲搜尋發現特定的混合流程最有效。最佳拓撲並非簡單結構，而是把反覆修訂過程與外部工具使用結合：一個預測代理會進行多輪反思，其程式碼則由執行代理運行測試案例驗證。這顯示對程式任務而言，結合自我修正與外部驗證的結構比簡單的多代理系統設計更優越。

![MASS: Multi-Agent System Search](../assets/MASS_Multi_Agent_System_Search.png)

圖4（作者授權）：多代理系統搜尋框架是一個三階段優化流程，探索涵蓋可優化提示（指令與示例）及可配置代理模組（聚合、反思、辯論、總結與工具使用）的搜尋空間。第一階段「模組層級提示優化」獨立優化每個代理模組的提示。第二階段「工作流程拓撲優化」則從影響力加權的設計空間中抽樣有效的系統配置並整合已優化的提示。最終階段「工作流程層級提示優化」在第二階段找出最佳流程後，對整個多代理系統進行第二輪提示優化。

**3. 工作流程層級提示優化（Workflow-Level Prompt Optimization）：** 最後階段針對整個系統的提示進行全局優化。在找出最佳拓撲後，會把提示視為單一整合體進行微調，確保協作得宜並最佳化代理間的相互依賴。例如在 DROP 資料集找到最佳拓撲後，最終優化階段會調整「預測者」代理的提示。完成後的提示極為詳盡，首先提供資料集摘要，指出其聚焦於「擷取式問答」及「數值資訊」，接着加入少量範例說明正確的問答行為，並把核心指示設定為高風險場景：「你是一個高度專業的人工智能，負責為即時新聞報導擷取關鍵數據，直播節目仰賴你的準確與速度。」這種結合元知識、示例與角色扮演的提示，專為最終工作流程調整以最大化準確度。

關鍵發現與原則：實驗顯示，由 MASS 優化的多代理系統，在多項任務上明顯優於現有的手動設計系統與其他自動化設計方法。有效多代理系統的三大設計原則如下：

* 在組合前，先用高品質提示優化個別代理。
* 透過組合具影響力的拓撲建構多代理系統，而非在不受限的搜尋空間盲目探索。
* 透過最後的工作流程層級聯合優化，建模並優化代理之間的相依性。

在探討關鍵推理技巧後，我們先檢視一項核心效能原則：大型語言模型的推論擴展定律（Scaling Inference Law）。此定律指出，隨著分配給模型的運算資源增加，其表現會可預測地提升。這項原則在複雜系統中十分明顯，例如深度研究（Deep Research），人工智能代理會把主題拆分成子問題、把網絡搜尋作為工具並綜合成果，自主完成調查。

**深度研究（Deep Research）** 指一類旨在擔任不知疲倦、細緻研究助理的代理工具。這個領域的主要平台包括 Perplexity AI、Google 的 Gemini 研究功能，以及 OpenAI 在 ChatGPT 中的進階功能（見圖5）。

![Google Deep Research for Information Gathering](../assets/Google_Deep_Research_for_Information_Gathering.png)

圖5：Google Deep Research 的資訊蒐集介面

這些工具帶來的根本改變，是搜尋流程本身。傳統搜尋即時給出連結，還要你自行整合資訊；Deep Research 則採用截然不同的模式。你給人工智能一個複雜問題，並給它一段「時間預算」，通常幾分鐘。作為回報，你會收到詳盡報告。

在這段時間內，人工智能會以代理方式代表你完成以下繁複步驟，這對人類而言極為耗時：

1. **初步探索：** 根據最初提示執行多個精準搜尋。
2. **推理與修正：** 閱讀及分析第一批結果，綜合發現並批判性地找出缺口、矛盾或需要更多細節之處。
3. **後續查詢：** 按照內部推理，進行更細緻的新搜尋以填補缺口並深化理解。
4. **最終綜整：** 經過數輪搜尋與推理後，將所有經驗證的資訊整理成單一、連貫且結構化的摘要。

這種系統化方法確保回應全面且推理充分，大幅提升資訊蒐集的效率與深度，從而促進更具代理性的決策。

## 推論擴展定律（Scaling Inference Law）

...

在後端的 .env 檔設定 API 金鑰後，可分別為後端（透過 `pip install .`）與前端（`npm install`）安裝依賴。可使用 `make dev` 同時啟動開發伺服器，或分別啟動。後端代理定義於 `backend/src/agent/graph.py`，負責產生初始搜尋查詢、執行網絡研究、分析知識缺口、反覆修正查詢並使用 Gemini 模型綜整帶引用的答案。正式部署時，後端伺服器會提供靜態前端建置，並需要 Redis 以串流即時輸出及 Postgres 資料庫管理資料。可透過 `docker-compose up` 建立並執行 Docker 映像，該示例亦需要在 `docker-compose.yml` 設定 LangSmith API 金鑰。此應用採用 React、Vite、Tailwind CSS、Shadcn UI、LangGraph 與 Google Gemini，並以 Apache License 2.0 授權。

| ``# Create our Agent Graph builder = StateGraph(OverallState, config_schema=Configuration) # Define the nodes we will cycle between builder.add_node("generate_query", generate_query) builder.add_node("web_research", web_research) builder.add_node("reflection", reflection) builder.add_node("finalize_answer", finalize_answer) # Set the entrypoint as `generate_query` # This means that this node is the first one called builder.add_edge(START, "generate_query") # Add conditional edge to continue with search queries in a parallel branch builder.add_conditional_edges(    "generate_query", continue_to_web_research, ["web_research"] ) # Reflect on the web research builder.add_edge("web_research", "reflection") # Evaluate the research builder.add_conditional_edges(    "reflection", evaluate_research, ["web_research", "finalize_answer"] ) # Finalize the answer builder.add_edge("finalize_answer", END) graph = builder.compile(name="pro-search-agent")`` |
| :---- |

圖4：LangGraph 中 DeepSearch 的示例（程式碼來自 `backend/src/agent/graph.py`）

## 代理在想什麼？（So, what do agents think?）

總結來說，代理的思考流程是一套結合推理與行動的結構化方法，讓代理可以明確規劃步驟、監察進度，並與外部工具互動以蒐集資訊。

代理的「思考」核心由強大的大型語言模型驅動，模型會生成一連串想法，指引後續行動。流程通常遵循「思考—行動—觀察」循環：

1. **思考（Thought）：** 代理先產生文字化的想法，拆解問題、擬定計劃或分析現況。這段內部獨白令代理的推理過程變得透明且可操控。
2. **行動（Action）：** 基於思考，代理從預先定義的離散選項中選取行動。例如在問答情境中，可能包含線上搜尋、從特定網頁擷取資訊或提供最終答案。
3. **觀察（Observation）：** 代理會收到環境回饋，例如搜尋結果或網頁內容。

這個循環會重複，每次觀察都會影響下一個想法，直至代理認為達成最終解決方案並執行「完成」動作。

此方法的效能仰賴底層大型語言模型的進階推理與規劃能力。為了引導代理，ReAct 框架常採用少量示例學習，把人類式問題解決的軌跡提供給模型，示範如何有效結合思考與行動解決相似任務。

代理思考的頻率可因任務而調整。對於依賴知識的推理任務（例如事實查證），通常在每次行動後穿插思考，以確保資訊蒐集與推理的邏輯流程；相反地，在需要大量行動的決策任務（例如導航模擬環境）中，可較少使用思考，由代理自行判斷何時需要再思考。

## 重點一覽(At a Glance)

**內容（What）：** 複雜問題往往不是單一直接答案就能處理，對人工智能造成巨大挑戰。核心問題是如何讓代理處理需要邏輯推論、拆解與策略規劃的多步任務。若欠缺結構化方法，代理可能無法處理細節，導致答案不準或不完整。這些進階推理方法旨在讓代理的內在「思考」過程清晰呈現，讓它能有系統地解決難題。

**原因（Why）：** 標準化的解決方案是一套推理技巧，為代理的解題流程提供結構化框架。鏈式思考（CoT）與思維樹（Tree-of-Thought，ToT）等方法引導大型語言模型拆解問題並探索多條解決路徑；自我修正（Self-Correction）讓答案可以反覆打磨，提高準確度；ReAct 等代理框架把推理與行動結合，令代理可與外部工具及環境互動，蒐集資訊並調整計劃。這種把明確推理、探索、修正及工具使用結合的方式，造就更穩健、透明且能力更強的人工智能系統。

**經驗法則（Rule of Thumb）：** 當問題太複雜，無法透過單次回答處理，需要拆解、多步邏輯、與外部資料來源或工具互動，或需要策略規劃與調整時，就應使用這些推理技巧。這些技巧特別適合需要展示「過程」與最終答案同樣重要的任務。

**視覺摘要（Visual Summary）：**

![Reasoning Design Pattern](../assets/Reasoning_Design_Pattern.png)

圖7：推理設計範式

## 重要重點（Key Takeaways）

* 透過把推理過程公開，代理可以制定透明的多步計劃，這是自主行動與建立使用者信任的基礎能力。
* ReAct 框架提供代理的核心運作循環，讓它不僅能推理，還能與外部工具互動，在環境中動態行動與調整。
* 推論擴展定律（Scaling Inference Law）顯示代理的表現不只取決於底層模型規模，還取決於給予的「思考時間」，讓它能更從容地作出高品質的自主行動。
* 鏈式思考（CoT）是代理的內部獨白，提供結構化方式把複雜目標拆成可管理的行動序列。
* 思維樹（Tree-of-Thought）與自我修正賦予代理關鍵的審議能力，得以評估多種策略、從錯誤回溯，並在執行前改進自身計劃。
* 辯論鏈（CoD）等協作框架象徵代理從單兵作戰走向多代理系統，讓團隊協同推理，解決更複雜的問題並減少個體偏差。
* 深度研究（Deep Research）等應用展示這些技巧如何匯聚成可自主執行複雜、長時間任務（如深入調查）的代理。
* 要打造高效代理團隊，MASS 等框架會自動化優化個別代理的提示及其互動方式，確保整體多代理系統表現最佳。
* 整合這些推理技巧，可建立不只是自動化，而是真正自主、可被信任去規劃、行動並在無人監督下解決複雜問題的代理。

## 總結（Conclusions）

現代人工智能正在從被動工具轉型為自主代理，透過結構化推理完成複雜目標。這種代理行為始於內部獨白，透過鏈式思考（CoT）等技巧，在行動前構思清晰計劃。真正的自主性需要審議能力，代理可藉由自我修正與思維樹（ToT）評估多種策略並自我改進。邁向完全代理化的關鍵，是 ReAct 框架讓代理超越思考，開始行動並使用外部工具，形成思考、行動、觀察的核心循環，使代理能根據環境回饋動態調整策略。

代理深度審議的能力來自推論擴展定律，亦即給予更多運算「思考時間」可直接轉化為更穩健的自主行動。下一個前沿是多代理系統，像辯論鏈（CoD）等框架可建立協作的代理社群，共同推理達成共同目標。這不只是理論，深度研究（Deep Research）等代理應用已證明它們可以替使用者自主執行複雜、多步的調查。總體目標是打造可靠且透明的自主代理，值得信賴地獨立管理並解決繁複問題。當我們把明確推理與行動能力結合，這些方法正完成人工智能向真正代理型解題者的轉變。

## References

Relevant research includes:

1. "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models" by Wei et al. (2022)
2. "Tree of Thoughts: Deliberate Problem Solving with Large Language Models" by Yao et al. (2023)
3. "Program-Aided Language Models" by Gao et al. (2023)
4. "ReAct: Synergizing Reasoning and Acting in Language Models" by Yao et al. (2023)
5. Inference Scaling Laws: An Empirical Analysis of Compute-Optimal Inference for LLM Problem-Solving, 2024
6. Multi-Agent Design: Optimizing Agents with Better Prompts and Topologies, [https://arxiv.org/abs/2502.02533](https://arxiv.org/abs/2502.02533)
