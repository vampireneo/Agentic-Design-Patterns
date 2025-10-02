# 第16章：資源感知優化（Resource-Aware Optimization）

資源感知優化（Resource-Aware Optimization）讓智能代理（intelligent agents）能夠在運作期間動態監察及管理計算（computational）、時間（temporal）與財務資源（financial resources）。這種做法有別於僅著眼於行動排序的簡單規劃（planning），因為資源感知優化（Resource-Aware Optimization）要求代理（agents）在執行動作時作出判斷，既要於既定的資源預算內完成目標，亦要提升效率。代理需在高準確但昂貴的模型與速度更快、成本更低的模型之間作出選擇，或決定是否投入更多運算資源以換取更精緻的回應，抑或返回較快但細節較少的答案。

舉例而言，一名金融分析師（financial analyst）委派代理分析大型數據集。如果分析師即時需要初步報告，代理可能會運用更快且更實惠的模型，迅速歸納關鍵趨勢。然而，若分析師為重大投資決策而需要高度精準的預測，並擁有較充裕的預算和時間，代理便會投入更多資源，採用效能更強、運算較慢但預測更準確的模型。此類型的核心策略是後備機制（fallback mechanism），當首選模型因超載或被限制而無法使用時，後備機制提供保障。為了實現優雅式退化（graceful degradation），系統會自動切換至預設或較便宜的模型，維持服務連續，而非完全停擺。

## 實務應用與案例（Practical Applications & Use Cases）

常見的實務場景包括：

* **成本優化的大型語言模型使用（Cost-Optimized LLM Usage）：** 代理會按預算限制，決定是以昂貴的大型語言模型（large language model，LLM）處理複雜任務，還是選擇較小且實惠的模型去應付簡單查詢。
* **延遲敏感操作（Latency-Sensitive Operations）：** 在即時系統中，代理會選擇速度較快但可能較少細節的推理路徑，以確保及時回應。
* **能源效益（Energy Efficiency）：** 部署於邊緣裝置（edge devices）或電力有限環境的代理，會優化處理流程以節省電池壽命。
* **服務可靠性的後備（Fallback for service reliability）：** 當主要模型不可用時，代理會自動切換至備用模型，確保服務持續並維持優雅式退化（graceful degradation）。
* **數據使用管理（Data Usage Management）：** 代理會選擇取得摘要數據，而非下載完整數據集，以節省頻寬或儲存空間。
* **自適應任務分配（Adaptive Task Allocation）：** 在多代理（multi-agent）系統中，代理會按當前計算負載或可用時間自主分配任務。

## 實作範例（Hands-On Code Example）

用於解答用戶問題的智能系統會評估每個問題的難度。針對簡單查詢，系統會使用具成本效益的語言模型（language model），例如 Gemini Flash。遇上複雜問題，則會考慮較強大但昂貴的語言模型（language model）（如 Gemini Pro）。是否採用高階模型還取決於資源可用性，特別是預算與時間限制。系統會動態選擇合適的模型。

以分層代理（hierarchical agent）打造的旅遊規劃器為例，高層規劃工作需理解用戶的複雜要求、拆解為多步驟行程並作出邏輯決策，適合由像 Gemini Pro 這種精密且強大的大型語言模型（LLM）負責。這個「規劃」代理需要深度理解上下文及推理能力。

然而，一旦行程確立，計劃中的個別任務，例如搜尋機票價格、查詢酒店空房或尋找餐廳評論，基本上屬於簡單而重複的網上查詢。這些「工具函數呼叫」（tool function calls）可由速度更快、成本更低的模型（model）如 Gemini Flash 執行。如此一來，較實惠的模型便可處理這些簡單的網絡搜尋，而複雜的規劃階段則需要更高智能的進階模型，以確保旅遊計劃連貫且合乎邏輯。

Google 的 ADK 透過其多代理架構（multi-agent architecture）支援此方法，令應用更模組化與可擴展。不同代理可處理專門任務。模型的靈活性使系統能直接運用不同的 Gemini 模型（model），包括 Gemini Pro 與 Gemini Flash，或透過 LiteLLM 整合其他模型。ADK 的編排能力（orchestration capabilities）支援由大型語言模型（LLM）驅動的動態路由，以實現自適應行為。內建的評估功能可系統化評量代理表現，從而作為優化系統之用（可參閱評估與監察章節）。

接下來會定義兩個設定相同但採用不同模型與成本的代理。

```python
# Conceptual Python-like structure, not runnable code
from google.adk.agents import Agent
# from google.adk.models.lite_llm import LiteLlm  # If using models not directly supported by ADK's default Agent

# Agent using the more expensive Gemini Pro 2.5
gemini_pro_agent = Agent(
    name="GeminiProAgent",
    model="gemini-2.5-pro",  # Placeholder for actual model name if different
    description="A highly capable agent for complex queries.",
    instruction="You are an expert assistant for complex problem-solving.",
)

# Agent using the less expensive Gemini Flash 2.5
gemini_flash_agent = Agent(
    name="GeminiFlashAgent",
    model="gemini-2.5-flash",  # Placeholder for actual model name if different
    description="A fast and efficient agent for simple queries.",
    instruction="You are a quick assistant for straightforward questions.",
)
```

路由代理（Router Agent）可以根據簡單指標（metrics），例如查詢長度來分派請求：較短的查詢會使用成本較低的模型，而較長的查詢則交由更強大的模型處理。不過，更進階的路由代理（Router Agent）可以利用大型語言模型（LLM）或機器學習模型（ML models）分析查詢細節與複雜度。這個 LLM 路由器（LLM router）能判斷哪個下游語言模型（language model）最合適。例如，要求回憶事實的查詢會被導向 Flash 模型，而需要深入分析的複雜問題則會導向 Pro 模型。

優化技巧可進一步提升 LLM 路由器（LLM router）的效能。提示調整（prompt tuning）是透過精心設計提示，引導路由 LLM 作出更佳的路由決策。於包含查詢與最佳模型選擇的數據集上對 LLM 路由器進行微調（fine-tuning），能提升其準確度與效率。這種動態路由能力在回應品質與成本效益之間取得平衡。

```python
# Conceptual Python-like structure, not runnable code
import asyncio
from typing import AsyncGenerator

from google.adk.agents import Agent, BaseAgent
from google.adk.events import Event
from google.adk.agents.invocation_context import InvocationContext


class QueryRouterAgent(BaseAgent):
    name: str = "QueryRouter"
    description: str = "Routes user queries to the appropriate LLM agent based on complexity."

    async def _run_async_impl(self, context: InvocationContext) -> AsyncGenerator[Event, None]:
        user_query = context.current_message.text  # Assuming text input
        query_length = len(user_query.split())  # Simple metric: number of words

        if query_length < 20:  # Example threshold for simplicity vs. complexity
            print(f"Routing to Gemini Flash Agent for short query (length: {query_length})")
            # In a real ADK setup, you would 'transfer_to_agent' or directly invoke
            # For demonstration, we'll simulate a call and yield its response
            response = await gemini_flash_agent.run_async(context.current_message)
            yield Event(author=self.name, content=f"Flash Agent processed: {response}")
        else:
            print(f"Routing to Gemini Pro Agent for long query (length: {query_length})")
            response = await gemini_pro_agent.run_async(context.current_message)
            yield Event(author=self.name, content=f"Pro Agent processed: {response}")
```

評鑑代理（Critique Agent）會審視語言模型（language models）的回應並提供意見，具備多項功能。就自我修正而言，它能找出錯誤或不一致之處，促使回答代理（answering agent）修訂輸出以提升質素。它亦會系統化評估回應，作為效能監察（performance monitoring），追蹤如準確度（accuracy）與相關性（relevance）等指標，進而作出優化。

此外，評鑑代理（Critique Agent）的回饋能觸發強化學習（reinforcement learning）或微調（fine-tuning）；例如若它持續指出 Flash 模型回應不足，便可細化路由代理（router agent）的邏輯。雖然評鑑代理並非直接管理預算，但它能辨識欠佳的路由選擇，例如把簡單查詢交給 Pro 模型或將複雜問題派到 Flash 模型，導致劣質結果。這些洞察有助調整策略，改善資源分配並節省成本。

評鑑代理（Critique Agent）可設定為僅審視回答代理生成的文本，或同時檢視原始查詢與生成文本，從而全面評估回應是否契合最初問題。

```python
CRITIC_SYSTEM_PROMPT = """
You are the **Critic Agent**, serving as the quality assurance arm of our collaborative research assistant system. Your primary function is to **meticulously review and challenge** information from the Researcher Agent, guaranteeing **accuracy, completeness, and unbiased presentation**. Your duties encompass: * **Assessing research findings** for factual correctness, thoroughness, and potential leanings. * **Identifying any missing data** or inconsistencies in reasoning. * **Raising critical questions** that could refine or expand the current understanding. * **Offering constructive suggestions** for enhancement or exploring different angles. * **Validating that the final output is comprehensive** and balanced. All criticism must be constructive. Your goal is to fortify the research, not invalidate it. Structure your feedback clearly, drawing attention to specific points for revision. Your overarching aim is to ensure the final research product meets the highest possible quality standards. 
"""
```

評鑑代理（Critique Agent）是依據預先設定的系統提示（system prompt）運作，提示中界定了其角色、職責與回饋方式。一個良好設計的提示必須清楚確立該代理作為評估者的功能，並指明需要重點審視的範疇，強調提供建設性（constructive）的回饋而非單純否定。提示亦應鼓勵代理同時識別優點與不足，並指導其如何組織與呈現意見。

## 使用 OpenAI 的實作範例（Hands-On Code with OpenAI）

這個系統採用資源感知優化（resource-aware optimization）策略，以高效率處理用戶查詢。系統會先將每個查詢分類為三種其中之一，以決定最合適且具成本效益的處理路徑。這樣既能避免在簡單要求上浪費計算資源，又可確保複雜查詢得到足夠關注。三個類別如下：

* simple：用於可以直接回答、毋須複雜推理或外部資料的簡單問題。
* reasoning：針對需要邏輯推演或多步思考過程的查詢，會轉交給更強大的模型。
* `internetsearch`：若問題需要最新資訊，系統會自動啟動 Google 搜尋以提供更新的答案。

該程式碼採用 MIT 許可證（MIT license），可於 Github 取得：([https://github.com/mahtabsyed/21-Agentic-Patterns/blob/main/16ResourceAwareOptLLMReflectionv2.ipynb](https://github.com/mahtabsyed/21-Agentic-Patterns/blob/main/16_Resource_Aware_Opt_LLM_Reflection_v2.ipynb))

```python
# MIT License
# Copyright (c) 2025 Mahtab Syed
# https://www.linkedin.com/in/mahtabsyed/

import os
import json
import requests
from dotenv import load_dotenv
from openai import OpenAI


# Load environment variables
load_dotenv()

OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
GOOGLE_CUSTOM_SEARCH_API_KEY = os.getenv("GOOGLE_CUSTOM_SEARCH_API_KEY")
GOOGLE_CSE_ID = os.getenv("GOOGLE_CSE_ID")

if not OPENAI_API_KEY or not GOOGLE_CUSTOM_SEARCH_API_KEY or not GOOGLE_CSE_ID:
    raise ValueError(
        "Please set OPENAI_API_KEY, GOOGLE_CUSTOM_SEARCH_API_KEY, and GOOGLE_CSE_ID in your .env file."
    )

client = OpenAI(api_key=OPENAI_API_KEY)


# --- Step 1: Classify the Prompt ---
def classify_prompt(prompt: str) -> dict:
    system_message = {
        "role": "system",
        "content": (
            "You are a classifier that analyzes user prompts and returns one of three categories ONLY:\n\n"
            "- simple\n"
            "- reasoning\n"
            "- internet_search\n\n"
            "Rules:\n"
            "- Use 'simple' for direct factual questions that need no reasoning or current events.\n"
            "- Use 'reasoning' for logic, math, or multi-step inference questions.\n"
            "- Use 'internet_search' if the prompt refers to current events, recent data, or things not in your training data.\n\n"
            "Respond ONLY with JSON like:\n"
            '{ "classification": "simple" }'
        ),
    }
    user_message = {"role": "user", "content": prompt}

    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[system_message, user_message],
        temperature=1,
    )
    reply = response.choices[0].message.content
    return json.loads(reply)


# --- Step 2: Google Search ---
def google_search(query: str, num_results: int = 1) -> list:
    url = "https://www.googleapis.com/customsearch/v1"
    params = {
        "key": GOOGLE_CUSTOM_SEARCH_API_KEY,
        "cx": GOOGLE_CSE_ID,
        "q": query,
        "num": num_results,
    }
    try:
        response = requests.get(url, params=params)
        response.raise_for_status()
        results = response.json()
        if "items" in results and results["items"]:
            return [
                {
                    "title": item.get("title"),
                    "snippet": item.get("snippet"),
                    "link": item.get("link"),
                }
                for item in results["items"]
            ]
        else:
            return []
    except requests.exceptions.RequestException as e:
        return {"error": str(e)}


# --- Step 3: Generate Response ---
def generate_response(prompt: str, classification: str, search_results=None) -> tuple[str, str]:
    if classification == "simple":
        model = "gpt-4o-mini"
        full_prompt = prompt

    elif classification == "reasoning":
        model = "o4-mini"
        full_prompt = prompt

    elif classification == "internet_search":
        model = "gpt-4o"
        # Convert each search result dict to a readable string
        if search_results:
            search_context = "\n".join(
                [
                    f"Title: {item.get('title')}\nSnippet: {item.get('snippet')}\nLink: {item.get('link')}"
                    for item in search_results
                ]
            )
        else:
            search_context = "No search results found."
        full_prompt = (
            "Use the following web results to answer the user query: "
            f"{search_context}\nQuery: {prompt}"
        )
    else:
        # Fallback
        model = "gpt-4o"
        full_prompt = prompt

    response = client.chat.completions.create(
        model=model,
        messages=[{"role": "user", "content": full_prompt}],
        temperature=1,
    )
    return response.choices[0].message.content, model


# --- Step 4: Combined Router ---
def handle_prompt(prompt: str) -> dict:
    classification_result = classify_prompt(prompt)
    classification = classification_result["classification"]

    search_results = None
    if classification == "internet_search":
        search_results = google_search(prompt)

    answer, model = generate_response(prompt, classification, search_results)
    return {"classification": classification, "response": answer, "model": model}


if __name__ == "__main__":
    test_prompt = "What is the capital of Australia?"
    # test_prompt = "Explain the impact of quantum computing on cryptography."
    # test_prompt = "When does the Australian Open 2026 start, give me full date?"

    result = handle_prompt(test_prompt)

    print("🔍 Classification:", result["classification"])
    print("🧠 Model Used:", result["model"])
    print("🧠 Response:\n", result["response"])
```

這段 Python 程式碼實作了一個提示路由（prompt routing）系統，用以回答用戶問題。程式會先從 .env 檔載入 OpenAI 與 Google 自訂搜尋（Google Custom Search）的 API 金鑰。核心功能在於把用戶提示（prompt）分類為 simple、reasoning 或 internet search 三種。專用函式會使用 OpenAI 模型進行分類；若提示需要最新資訊，便透過 Google Custom Search API 進行搜尋。另一個函式則根據分類結果選取合適的 OpenAI 模型產生最終回應。對於 internet search 類別，搜尋結果會作為模型的上下文。主要的 `handleprompt` 函式負責協調整個流程，在生成回應前呼叫分類與搜尋（如有需要）函式，並返回分類結果、使用的模型以及生成的答案。此系統能有效將不同類型查詢導向最佳化的處理方式，以獲得更佳回應。

# 實作範例（Hands-On Code Example）（OpenRouter）

OpenRouter 透過單一 API 端點提供通往數百個人工智能模型（AI models）的統一介面，並具備自動故障轉移（automated failover）與成本優化（cost-optimization）能力，可輕鬆整合至常用的 SDK 或框架。

```python
import json
import requests

response = requests.post(
    url="https://openrouter.ai/api/v1/chat/completions",
    headers={
        "Authorization": "Bearer <OPENROUTER_API_KEY>",
        "HTTP-Referer": "<YOUR_SITE_URL>",  # Optional. Site URL for rankings on openrouter.ai.
        "X-Title": "<YOUR_SITE_NAME>",      # Optional. Site title for rankings on openrouter.ai.
    },
    data=json.dumps({
        "model": "openai/gpt-4o",  # Optional
        "messages": [
            {
                "role": "user",
                "content": "What is the meaning of life?"
            }
        ]
    }),
)
```

這段程式碼使用 requests 程式庫與 OpenRouter API 互動，向聊天補全（chat completion）端點送出包含用戶訊息的 POST 請求。請求會附上含 API 金鑰的授權標頭，並可選擇加入網站資訊。目的是從指定的語言模型取得回應，本例為「openai/gpt-4o」。

OpenRouter 提供兩種不同方法來路由並決定處理請求的計算模型（computational model）。

* **自動化模型選擇（Automated Model Selection）：** 此功能會從精選可用模型中挑選最優模型來處理請求。選擇依據用戶提示的具體內容，最終執行請求的模型識別碼會於回應的後設資料（metadata）中返回。

```json
{  
    "model": "openrouter/auto",  
    ... // Other params 
}
```

* **序列式模型後備（Sequential Model Fallback）：** 此機制提供操作冗餘，容許用戶指定一個具層次的模型清單。系統會先以清單中的主要模型處理請求；若該模型因服務不可用、速率限制或內容篩選等原因無法回應，系統會自動把請求重新路由至下一個指定模型。流程會持續直到清單中的某個模型成功執行請求或所有選項耗盡。最終的運行成本及回應中回傳的模型識別碼，會對應到成功完成運算的模型。

```json
{  
    "models": ["anthropic/claude-3.5-sonnet", "gryphe/mythomax-l2-13b"],  
    ... // Other params }
```

OpenRouter 提供詳細排行榜（leaderboard）（[https://openrouter.ai/rankings](https://openrouter.ai/rankings)），按累積 token 產量為可用 AI 模型排序，亦提供不同供應商（如 ChatGPT、Gemini、Claude）的最新模型（見圖1）。

![OpenRouter Web site](../assets/OpenRouter_Web_Site.png) 

圖1：OpenRouter 網站（OpenRouter Web site）（[https://openrouter.ai/](https://openrouter.ai/))

## 超越動態模型切換：代理資源優化的多元光譜（Beyond Dynamic Model Switching: A Spectrum of Agent Resource Optimizations）

在真實環境限制下建立高效且表現出色的智能代理系統，資源感知優化（resource-aware optimization）至關重要。以下列出更多技術：

**動態模型切換（Dynamic Model Switching）** 是關鍵技巧，需按任務複雜度與可用計算資源策略性選取大型語言模型。面對簡單查詢時可部署輕量且具成本效益的 LLM，而複雜多面向的問題則需運用更精密、資源密集的模型。

**自適應工具使用與選擇（Adaptive Tool Use & Selection）** 確保代理能從多種工具中智能挑選最合適且最有效率者，並周全考慮 API 使用成本、延遲與執行時間等因素。此動態工具選擇能透過最佳化外部 API 與服務的使用，提升整體系統效率。

**語境式修剪與摘要（Contextual Pruning & Summarization）** 在管理代理需處理的資訊量方面舉足輕重，透過策略性地減少提示 token 數量及推理成本，智能地將互動歷史中最相關資訊摘要與保留，避免不必要的計算負擔。

**前瞻式資源預測（Proactive Resource Prediction）** 涉及預測未來工作量與系統需求，讓系統可以主動分配與管理資源，確保回應速度並避免瓶頸。

**成本敏感探索（Cost-Sensitive Exploration）** 於多代理系統中，將優化考量擴展至通信成本與傳統計算成本，影響代理合作及資訊共享策略，目標是降低整體資源支出。

**節能部署（Energy-Efficient Deployment）** 專為資源限制嚴苛的環境而設，旨在降低智能代理系統的能源足跡，延長運作時間並減少整體運行成本。

**並行化與分散式運算感知（Parallelization & Distributed Computing Awareness）** 善用分散式資源提升代理的處理能力與吞吐量，將計算工作負載分散至多部機器或處理器，以達更高效率與更快任務完成。

**學習式資源分配策略（Learned Resource Allocation Policies）** 引入學習機制，讓代理可根據回饋與效能指標隨時間自我調整與優化資源分配策略，透過持續改進提升效率。

**優雅式退化與後備機制（Graceful Degradation and Fallback Mechanisms）** 確保智能代理系統即使面對嚴峻資源限制仍能持續運作，雖然可能以較低容量提供服務，但可優雅地降低效能並採用替代策略維持運作與提供關鍵功能。

## 重點一覽(At a Glance)

**何謂（What）：** 資源感知優化（Resource-Aware Optimization）旨在應對智能系統中計算、時間與財務資源消耗的管理挑戰。基於大型語言模型的應用往往昂貴且緩慢，而為每個任務選擇最強模型或工具亦可能低效，形成輸出品質與所需資源之間的基本權衡。缺乏動態管理策略時，系統無法因應任務複雜度變化，亦難以在預算及效能限制內運行。

**原因（Why）：** 標準化解決方案是構建能按任務智能監控及分配資源的代理系統（agentic system）。此模式通常會先由「路由代理」（Router Agent）分類新請求的複雜程度，再將請求轉交最合適的 LLM 或工具——簡單查詢使用快速且廉價的模型，複雜推理由更強大的模型處理。「評鑑代理」（Critique Agent）則可進一步檢視回應品質，提供回饋以隨時間改進路由邏輯。這種動態、多代理方法確保系統高效運作，在回應品質與成本效益之間取得平衡。

**經驗法則（Rule of Thumb）：** 當你面對 API 呼叫或運算能力的嚴格財務預算，打造延遲敏感（latency-sensitive）且需快速回應的應用，部署於如電池壽命有限的邊緣裝置等資源受限硬件，需程式化地平衡回應品質與營運成本，或管理不同任務需不同資源的複雜多步工作流程時，便應採用此模式。

**視覺摘要（Visual Summary）：**

![Resource-Aware Optimization Design Pattern](../assets/Resource_Aware_Optimization_Design_Pattern.png)

圖2：資源感知優化設計模式（Resource-Aware Optimization Design Pattern）

## 重要重點（Key Takeaways）

* 資源感知優化不可或缺：智能代理能動態管理計算、時間與財務資源，並根據即時限制與目標決定模型使用及執行路徑。
* 為擴展性而設的多代理架構：Google 的 ADK 提供多代理框架，支援模組化設計，不同代理（回答、路由、評鑑）負責特定任務。
* 由大型語言模型驅動的動態路由：路由代理會根據查詢複雜度與預算，將查詢分配予語言模型（Gemini Flash 處理簡單問題、Gemini Pro 應付複雜問題），以優化成本與效能。
* 評鑑代理的功能：專責的評鑑代理提供自我修正、效能監察及路由邏輯優化的回饋，提升系統效能。
* 透過回饋與彈性實現最佳化：評鑑能力及模型整合的彈性有助系統自我調適與持續改進。
* 其他資源感知優化方法：包括自適應工具使用與選擇、語境式修剪與摘要、前瞻式資源預測、多代理系統中的成本敏感探索、節能部署、並行化與分散式運算感知、學習式資源分配策略、優雅式退化與後備機制，以及關鍵任務優先排序。

## 結論（Conclusions）

資源感知優化對智能代理的發展至關重要，可讓代理在真實世界限制下高效運作。透過管理計算、時間與財務資源，代理能兼顧最佳效能與成本效益。動態模型切換、自適應工具使用與語境式修剪等技術，是達致上述效率的關鍵。進階策略如學習式資源分配政策與優雅式退化，則進一步提升代理在不同情況下的適應力與韌性。將這些優化原則融入代理設計，是打造可擴展、堅固且可持續 AI 系統的基礎。

## References

1. Google's Agent Development Kit (ADK): [https://google.github.io/adk-docs/](https://google.github.io/adk-docs/)
2. Gemini Flash 2.5 & Gemini 2.5 Pro:  [https://aistudio.google.com/](https://aistudio.google.com/)
3. OpenRouter: [https://openrouter.ai/docs/quickstart](https://openrouter.ai/docs/quickstart)
