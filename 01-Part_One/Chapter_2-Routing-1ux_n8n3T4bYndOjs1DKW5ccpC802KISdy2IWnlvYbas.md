# 第二章：路由(Routing)

## 路由模式(Routing Pattern)概覽

雖然透過提示鏈(prompt chaining)進行的順序處理是運行具決定性、線性工作流程的基礎技術，但在需要自適應反應的情境下，它的適用性有限。現實世界中的代理系統(agentic system)往往必須根據環境狀態、使用者輸入或前一個操作結果等偶發因素，在多個潛在行動之間仲裁。這種能夠動態決策、將流程導向不同專門功能、工具或子程序(sub-process)的能力，是透過稱為路由(routing)的機制實現。

路由(routing)將條件邏輯(conditional logic)引入代理(agents)的操作框架，使系統從固定執行路徑轉向由代理動態評估特定條件，再從一系列可能的後續行動中作出選擇的模式。這種方式讓系統行為更加靈活並具備情境感知。

例如，一個為客戶查詢而設計的代理，只要配備路由(routing)功能，就能先分類傳入問題以判斷使用者意圖。根據分類結果，它可以將查詢導向專門的直接答問代理、用於帳戶資訊的資料庫檢索工具，或處理複雜問題的升級程序，而不是預設走單一、預先決定的回應路徑。因此，一個使用路由(routing)的更複雜代理可以：

1. 分析使用者的查詢。
2. **根據意圖(intent)路由(route)** 查詢：
   * 如果意圖是「查詢訂單狀態」，就路由至與訂單資料庫互動的子代理或工具鏈(tool chain)。
   * 如果意圖是「產品資訊」，就路由至搜尋產品目錄的子代理或鏈(chain)。
   * 如果意圖是「技術支援」，就路由至存取疑難排解指南或升級至真人的人員的另一條鏈(chain)。
   * 如果意圖不明，就路由至澄清子代理或提示鏈(prompt chain)。

路由模式(Routing pattern)的核心組件是執行評估並引導流程的機制。這個機制可以透過多種方式實作：

* **基於大型語言模型(LLM)的路由(LLM-based Routing)：** 語言模型(language model)本身可以透過提示(prompt)來分析輸入，並輸出指出下一步或目的地的特定識別碼或指令。例如，提示可以要求大型語言模型(LLM)「分析以下使用者查詢，並只輸出分類：'Order Status'、'Product Info'、'Technical Support' 或 'Other'。」代理系統會讀取這個輸出，並據此導引工作流程。
* **基於嵌入(embedding)的路由(Embedding-based Routing)：** 輸入查詢可以轉換成向量嵌入(vector embedding)（詳見第14章的檢索增強生成(RAG)）。這個嵌入會與代表不同路徑或功能的嵌入進行比較，查詢會路由至嵌入最相似的路徑。這對於語意路由(semantic routing)很有用，因為決策是基於輸入的意義而不只是關鍵字。
* **基於規則(rule-based)的路由(Rule-based Routing)：** 這涉及使用預先定義的規則或邏輯（例如 if-else 陳述、switch case），依據輸入中的關鍵字、模式或結構化資料進行判斷。這比基於大型語言模型(LLM)的路由更快速、更具決定性，但在處理細膩或新奇輸入時較缺乏彈性。
* **基於機器學習模型(machine learning model)的路由(Machine Learning Model-Based Routing)：** 它採用在一小份標註資料上專門訓練的辨別模型(classifier)，以執行路由任務。儘管在概念上與基於嵌入的方法相似，其關鍵特徵是監督式微調(supervised fine-tuning)流程，會調整模型參數以建立專門的路由功能。這種技術不同於基於大型語言模型(LLM)的路由，因為決策元件不是在推理時執行提示的生成式模型(generative model)。相反地，路由邏輯被編碼在微調後模型的權重(weights)之中。大型語言模型(LLM)可能在前處理步驟中用來產生合成資料，以擴增訓練集，但並未參與即時的路由決策。

路由機制可以在代理操作週期的多個節點實施。它們可以在一開始用於分類主要任務，在處理鏈(chain)的中間點決定後續行動，或於子程序(subroutine)中自一組工具(tool)選出最合適的工具。

像 LangChain、LangGraph 以及 Google 的代理開發套件(Agent Developer Kit，ADK)等運算框架，提供明確的結構來定義與管理此類條件邏輯(conditional logic)。LangGraph 透過其基於狀態(state-based)的圖形架構(graph architecture)，特別適合處理決策依賴整個系統累積狀態的複雜路由情境。同樣地，Google 的 ADK 提供用來建構代理能力與互動模型的基礎元件，作為實作路由邏輯的基礎。在這些框架的執行環境中，開發者會定義可能的操作路徑，以及決定在計算圖(computational graph)節點之間轉換的功能或基於模型的評估。

路由(routing)的實作讓系統得以超越決定性的順序處理。它促進更具適應性的執行流程開發，讓系統能動態且適切地回應更廣泛的輸入與狀態變化。

## 實務應用與使用情境(Practical Applications & Use Cases)

路由模式(Routing pattern)是設計自適應代理系統的關鍵控制機制，使系統能根據多變的輸入和內部狀態動態調整執行路徑。透過提供必要的條件邏輯層，它的效用遍及多個領域。

在人機互動(human-computer interaction)領域，例如虛擬助理或人工智慧教學系統中，路由(routing)被用來詮釋使用者意圖。對自然語言查詢的初步分析會決定最適合的下一步行動，無論是呼叫特定的資訊檢索工具、升級至真人服務人員，或根據使用者表現選擇課程中的下一個模組。這讓系統能超越線性的對話流程，做出具情境的回應。

在自動化資料與文件處理流程中，路由(routing)扮演分類與分發功能。傳入資料（例如電子郵件、支援單或 API 載荷）會根據內容、中繼資料(metadata)或格式進行分析。系統會將每個項目導向相應的工作流程，例如銷售機會匯入流程、針對 JSON 或 CSV 格式的特定資料轉換功能，或緊急議題的升級通道。

在涉及多個專門工具或代理的複雜系統中，路由(routing)扮演高階調度器(dispatcher)。一個包含搜尋、摘要及分析資訊的獨立代理的研究系統，會使用路由器(router)根據當前目標，將任務指派給最合適的代理。同樣地，人工智慧編碼助理會利用路由(routing)，先辨識程式語言與使用者意圖（除錯、說明或翻譯），再把程式碼片段交給正確的專門工具。

最終，路由(routing)提供了邏輯仲裁(logical arbitration)的能力，是建立功能多樣且具情境感知系統的關鍵。它將代理從執行預先定義序列的靜態角色，轉變為在變動情況下能決定最有效完成任務方式的動態系統。

## 實作範例：LangChain(Hands-On Code Example (LangChain))

在程式中實作路由(routing)涉及定義可能路徑以及決定該走哪條路的邏輯。像 LangChain 與 LangGraph 等框架提供針對此用途的特定元件與結構。LangGraph 的狀態式圖形結構特別直觀，適合用於視覺化與實作路由邏輯。

以下程式碼示範了一個使用 LangChain 與 Google 生成式人工智慧(Generative AI)的簡單代理式系統。它建立一個「協調者(coordinator)」，依據請求意圖（訂位、資訊或不清楚），將使用者請求路由至不同的模擬「子代理(sub-agent)」處理器。系統利用語言模型(language model)分類請求，然後委派至適當的處理函式，模擬多代理架構中常見的基本委派模式。

首先，請確保安裝必要的程式庫：

```bash
pip install langchain langgraph google-cloud-aiplatform langchain-google-genai google-adk deprecated pydantic
```

你還需要為所選的語言模型設定環境中的 API 金鑰（例如 OpenAI、Google Gemini、Anthropic）。

```python
# Copyright (c) 2025 Marco Fago
# https://www.linkedin.com/in/marco-fago/
#
# This code is licensed under the MIT License.
# See the LICENSE file in the repository for the full license text.

from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableBranch


# --- Configuration ---
# Ensure your API key environment variable is set (e.g., GOOGLE_API_KEY)
try:
    llm = ChatGoogleGenerativeAI(model="gemini-2.5-flash", temperature=0)
    print(f"Language model initialized: {llm.model}")
except Exception as e:
    print(f"Error initializing language model: {e}")
    llm = None


# --- Define Simulated Sub-Agent Handlers (equivalent to ADK sub_agents) ---
def booking_handler(request: str) -> str:
    """Simulates the Booking Agent handling a request."""
    print("\n--- DELEGATING TO BOOKING HANDLER ---")
    return f"Booking Handler processed request: '{request}'. Result: Simulated booking action."


def info_handler(request: str) -> str:
    """Simulates the Info Agent handling a request."""
    print("\n--- DELEGATING TO INFO HANDLER ---")
    return f"Info Handler processed request: '{request}'. Result: Simulated information retrieval."


def unclear_handler(request: str) -> str:
    """Handles requests that couldn't be delegated."""
    print("\n--- HANDLING UNCLEAR REQUEST ---")
    return f"Coordinator could not delegate request: '{request}'. Please clarify."


# --- Define Coordinator Router Chain (equivalent to ADK coordinator's instruction) ---
# This chain decides which handler to delegate to.
coordinator_router_prompt = ChatPromptTemplate.from_messages([
    (
        "system",
        """Analyze the user's request and determine which specialist handler should process it.
        - If the request is related to booking flights or hotels,
           output 'booker'.
        - For all other general information questions, output 'info'.
        - If the request is unclear or doesn't fit either category,
           output 'unclear'.
        ONLY output one word: 'booker', 'info', or 'unclear'."""
    ),
    ("user", "{request}")
])

if llm:
    coordinator_router_chain = coordinator_router_prompt | llm | StrOutputParser()


# --- Define the Delegation Logic (equivalent to ADK's Auto-Flow based on sub_agents) ---
# Use RunnableBranch to route based on the router chain's output.

# Define the branches for the RunnableBranch
branches = {
    "booker": RunnablePassthrough.assign(
        output=lambda x: booking_handler(x['request']['request'])
    ),
    "info": RunnablePassthrough.assign(
        output=lambda x: info_handler(x['request']['request'])
    ),
    "unclear": RunnablePassthrough.assign(
        output=lambda x: unclear_handler(x['request']['request'])
    ),
}

# Create the RunnableBranch. It takes the output of the router chain
# and routes the original input ('request') to the corresponding handler.
delegation_branch = RunnableBranch(
    (lambda x: x['decision'].strip() == 'booker', branches["booker"]),  # Added .strip()
    (lambda x: x['decision'].strip() == 'info', branches["info"]),      # Added .strip()
    branches["unclear"]  # Default branch for 'unclear' or any other output
)

# Combine the router chain and the delegation branch into a single runnable
# The router chain's output ('decision') is passed along with the original input ('request')
# to the delegation_branch.
coordinator_agent = {
    "decision": coordinator_router_chain,
    "request": RunnablePassthrough()
} | delegation_branch | (lambda x: x['output'])  # Extract the final output


# --- Example Usage ---
def main():
    if not llm:
        print("\nSkipping execution due to LLM initialization failure.")
        return

    print("--- Running with a booking request ---")
    request_a = "Book me a flight to London."
    result_a = coordinator_agent.invoke({"request": request_a})
    print(f"Final Result A: {result_a}")

    print("\n--- Running with an info request ---")
    request_b = "What is the capital of Italy?"
    result_b = coordinator_agent.invoke({"request": request_b})
    print(f"Final Result B: {result_b}")

    print("\n--- Running with an unclear request ---")
    request_c = "Tell me about quantum physics."
    result_c = coordinator_agent.invoke({"request": request_c})
    print(f"Final Result C: {result_c}")


if __name__ == "__main__":
    main()
```

如前所述，這段 Python 程式碼使用 LangChain 函式庫與 Google 的生成式人工智慧模型(Generative AI model)，特別是 gemini-2.5-flash，建構了一個簡單的代理式系統。它定義三個模擬子代理(sub-agent)處理器：`booking_handler`、`info_handler` 與 `unclear_handler`，各自用於處理特定類型的請求。

核心組件是 `coordinator_router_chain`，它利用 ChatPromptTemplate 指示語言模型(language model)將傳入使用者請求分類為 `booker`、`info` 或 `unclear`。這個路由鏈(router chain)的輸出接著由 RunnableBranch 使用，將原始請求委派至相應的處理函式。RunnableBranch 會檢查語言模型(language model)的決策，並將請求資料導向 `booking_handler`、`info_handler` 或 `unclear_handler`。`coordinator_agent` 將這些元件組合起來，先將請求送往路由決策，再把請求交給選定的處理器，最後擷取處理器回應作為輸出。

主函式(main function)透過三個範例請求示範系統的運作，展示不同輸入如何被路由並由模擬代理處理。同時也加入語言模型(language model)初始化的錯誤處理，以確保穩健性。整體程式結構模擬了一個基本的多代理框架，其中中央協調者將任務委派給根據意圖(intent)專門化的代理。

## 實作範例：Google ADK(Hands-On Code Example (Google ADK))

代理開發套件(Agent Development Kit，ADK)是一個用於工程化代理系統的框架，提供結構化環境以定義代理的能力與行為。相較於基於明確計算圖(computational graph)的架構，在 ADK 範式中，路由(routing)通常透過定義代表代理功能的一組「工具(tools)」來實作。框架的內部邏輯會管理針對使用者查詢選擇適當工具的過程，並透過底層模型(model)將使用者意圖對應至正確的功能處理器(handler)。

以下 Python 程式碼示範如何使用 Google 的 ADK 函式庫建立代理開發套件(ADK)應用。它設定一個「協調者(Coordinator)」代理，依據定義的指示將使用者請求路由至專門子代理(`"Booker"` 用於訂位，`"Info"` 用於一般資訊)。子代理再使用特定工具(tools)模擬處理請求，展示代理系統內的基本委派模式。

```python
# Copyright (c) 2025 Marco Fago
#
# This code is licensed under the MIT License.
# See the LICENSE file in the repository for the full license text.

import uuid
from typing import Dict, Any, Optional

from google.adk.agents import Agent
from google.adk.runners import InMemoryRunner
from google.adk.tools import FunctionTool
from google.genai import types
from google.adk.events import Event


# --- Define Tool Functions ---
# These functions simulate the actions of the specialist agents.
def booking_handler(request: str) -> str:
    """
    Handles booking requests for flights and hotels.

    Args:
        request: The user's request for a booking.

    Returns:
        A confirmation message that the booking was handled.
    """
    print("-------------------------- Booking Handler Called ----------------------------")
    return f"Booking action for '{request}' has been simulated."


def info_handler(request: str) -> str:
    """
    Handles general information requests.

    Args:
        request: The user's question.

    Returns:
        A message indicating the information request was handled.
    """
    print("-------------------------- Info Handler Called ----------------------------")
    return f"Information request for '{request}'. Result: Simulated information retrieval."


def unclear_handler(request: str) -> str:
    """Handles requests that couldn't be delegated."""
    return f"Coordinator could not delegate request: '{request}'. Please clarify."


# --- Create Tools from Functions ---
booking_tool = FunctionTool(booking_handler)
info_tool = FunctionTool(info_handler)

# Define specialized sub-agents equipped with their respective tools
booking_agent = Agent(
    name="Booker",
    model="gemini-2.0-flash",
    description="A specialized agent that handles all flight "
                "and hotel booking requests by calling the booking tool.",
    tools=[booking_tool],
)

info_agent = Agent(
    name="Info",
    model="gemini-2.0-flash",
    description="A specialized agent that provides general information "
                "and answers user questions by calling the info tool.",
    tools=[info_tool],
)

# Define the parent agent with explicit delegation instructions
coordinator = Agent(
    name="Coordinator",
    model="gemini-2.0-flash",
    instruction=(
        "You are the main coordinator. Your only task is to analyze "
        "incoming user requests "
        "and delegate them to the appropriate specialist agent. Do not try to answer the user directly.\n"
        "- For any requests related to booking flights or hotels, delegate to the 'Booker' agent.\n"
        "- For all other general information questions, delegate to the 'Info' agent."
    ),
    description="A coordinator that routes user requests to the correct specialist agent.",
    # The presence of sub_agents enables LLM-driven delegation (Auto-Flow) by default.
    sub_agents=[booking_agent, info_agent],
)


# --- Execution Logic ---
async def run_coordinator(runner: InMemoryRunner, request: str):
    """Runs the coordinator agent with a given request and delegates."""
    print(f"\n--- Running Coordinator with request: '{request}' ---")
    final_result = ""
    try:
        user_id = "user_123"
        session_id = str(uuid.uuid4())

        await runner.session_service.create_session(
            app_name=runner.app_name,
            user_id=user_id,
            session_id=session_id,
        )

        for event in runner.run(
            user_id=user_id,
            session_id=session_id,
            new_message=types.Content(
                role='user',
                parts=[types.Part(text=request)],
            ),
        ):
            if event.is_final_response() and event.content:
                # Try to get text directly from event.content to avoid iterating parts
                if hasattr(event.content, 'text') and event.content.text:
                    final_result = event.content.text
                elif event.content.parts:
                    # Fallback: Iterate through parts and extract text (might trigger warning)
                    text_parts = [part.text for part in event.content.parts if getattr(part, "text", None)]
                    final_result = "".join(text_parts)
                # Assuming the loop should break after the final response
                break

        print(f"Coordinator Final Response: {final_result}")
        return final_result

    except Exception as e:
        print(f"An error occurred while processing your request: {e}")
        return f"An error occurred while processing your request: {e}"


async def main():
    """Main function to run the ADK example."""
    print("--- Google ADK Routing Example (ADK Auto-Flow Style) ---")
    print("Note: This requires Google ADK installed and authenticated.")

    runner = InMemoryRunner(coordinator)

    # Example Usage
    result_a = await run_coordinator(runner, "Book me a hotel in Paris.")
    print(f"Final Output A: {result_a}")

    result_b = await run_coordinator(runner, "What is the highest mountain in the world?")
    print(f"Final Output B: {result_b}")

    result_c = await run_coordinator(runner, "Tell me a random fact.")  # Should go to Info
    print(f"Final Output C: {result_c}")

    result_d = await run_coordinator(runner, "Find flights to Tokyo next month.")  # Should go to Booker
    print(f"Final Output D: {result_d}")


if __name__ == "__main__":
    import nest_asyncio

    nest_asyncio.apply()
    await main()
```

這個腳本由一個主要的協調者(Coordinator)代理和兩個專門的 `sub_agents`（Booker 與 Info）組成。每個專門代理都配置一個包裝 Python 函式、模擬操作的 FunctionTool。`booking_handler` 函式模擬處理航班與飯店訂位，而 `info_handler` 函式模擬擷取一般資訊。`unclear_handler` 則作為協調者無法委派時的後備方案，雖然目前協調者邏輯在 `run_coordinator` 主流程中沒有明確使用它處理委派失敗。

協調者(Coordinator)代理的主要職責，如其指示(instruction)所述，是分析傳入的使用者訊息並將其委派給 Booker 或 Info 代理。由於協調者定義了 `sub_agents`，ADK 的 Auto-Flow 機制會自動處理這項委派。`run_coordinator` 函式會建立 InMemoryRunner，創建使用者與工作階段(session)識別碼，然後使用 runner 處理協調者代理的使用者請求。`runner.run` 方法會處理請求並產生事件(event)，程式會從 event.content 中擷取最終回應文字。

主函式(main function)透過不同的請求示範系統運作，展示如何把訂位請求委派給 Booker，以及將資訊請求委派給 Info 代理。

## 快速總結(At a Glance)

**重點是什麼(What)：** 代理系統(agentic system)經常必須回應無法由單一線性流程處理的各式輸入與情境。單純的順序工作流程缺乏依情境進行決策的能力。若沒有挑選特定任務所需工具或子程序(sub-process)的機制，系統就會維持僵化且缺乏適應性。這種限制使得難以建立能處理現實世界使用者請求複雜度與多變性的進階應用。

**為什麼(Why)：** 路由模式(Routing pattern)透過在代理操作框架中引入條件邏輯(conditional logic)，提供標準化解方。系統會先分析傳入查詢以判斷其意圖(intent)或本質，並根據分析結果動態將控制流程導向最適合的專門工具、功能或子代理。這項決策可以由提示大型語言模型(LLM)、套用預先定義的規則，或使用基於嵌入的語意相似度(semantic similarity)來驅動。最終，路由(routing)將靜態、預先決定的執行路徑，轉化為能夠情境感知且可選擇最佳行動的彈性工作流程。

**經驗法則(Rule of Thumb)：** 當代理需要依據使用者輸入或當前狀態，在多個截然不同的工作流程、工具或子代理之間做出選擇時，就應使用路由模式(Routing pattern)。它對必須分流或分類傳入請求以處理不同任務類型的應用至關重要，例如客服機器人區分銷售詢問、技術支援與帳戶管理問題。

**視覺摘要(Visual Summary)：**

![Router Pattern, using LLM as a Router](../assets/Router_Pattern_Using_LLM_as_a_Router.png)

圖1：使用大型語言模型(LLM)作為路由器(router)的路由模式(Router pattern)

## 重要重點(Key Takeaways)

* 路由(routing)讓代理能根據條件為工作流程的下一步做動態決策。
* 它讓代理能處理多樣輸入並調整行為，進而超越線性執行。
* 路由邏輯可以使用大型語言模型(LLM)、基於規則的系統或嵌入相似度(embedding similarity)來實作。
* LangGraph 與 Google ADK 等框架提供結構化方式，在代理工作流程中定義與管理路由(routing)，雖然採用不同的架構方法。

## 結論(Conclusion)

路由模式(Routing pattern)是在打造真正動態且具回應能力的代理系統時的關鍵一步。透過實作路由(routing)，我們得以超越簡單的線性執行流程，並賦予代理智慧決策能力，判斷如何處理資訊、回應使用者輸入，以及善用可用工具或子代理。

我們已看到路由(routing)如何應用於不同領域，從客服聊天機器人到複雜的資料處理管線。分析輸入並有條件地導引工作流程，是建立能處理現實世界任務變異性的代理的基礎。

使用 LangChain 與 Google ADK 的程式碼示例，展示了兩種不同但同樣有效的路由實作方法。LangGraph 的圖形化結構提供視覺化且明確的方式來定義狀態與轉換，特別適合包含複雜路由邏輯的多步驟工作流程。相對地，Google ADK 常著重於定義獨立能力（工具），並仰賴框架將使用者請求路由至適當的工具處理器(handler)，這對具備明確離散行動集的代理而言較為簡潔。

精通路由模式(Routing pattern)是建立能聰明應對不同情境、並依據脈絡提供量身回應或行動的代理的關鍵。它是打造多才多藝且穩健代理應用的重要組成。

## References

1. LangGraph Documentation: [https://www.langchain.com/](https://www.langchain.com/)
2. Google Agent Developer Kit Documentation: [https://google.github.io/adk-docs/](https://google.github.io/adk-docs/)
