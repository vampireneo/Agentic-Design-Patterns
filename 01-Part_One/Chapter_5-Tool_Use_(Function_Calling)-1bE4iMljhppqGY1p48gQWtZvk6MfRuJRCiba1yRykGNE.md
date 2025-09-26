# 第5章：工具使用（Tool Use）與函數調用（Function Calling）

## 工具使用模式（Tool Use Pattern）概覽

到目前為止，我們已經討論過幾種主要專注於協調大型語言模型（Large Language Models，LLM）互動及管理代理內部工作流程資訊流的代理型設計模式（Agentic patterns），例如鏈結（Chaining）、路由（Routing）、平行化（Parallelization）與反思（Reflection）。然而，若要讓代理真正有用並能與現實世界或外部系統互動，它們必須具備使用工具（Tools）的能力。

工具使用模式（Tool Use pattern），通常透過稱為函數調用（Function Calling）的機制來實現，讓代理可以與外部應用程式介面（APIs）、資料庫、服務甚至執行程式碼互動。它讓代理核心的 LLM 能夠根據使用者請求或任務當前狀態，決定何時以及如何使用特定外部函數。

整個流程一般包括：

1. **工具定義：** 向 LLM 定義並描述外部函數或能力。這些描述會包含函數的目的、名稱，以及它所接受的參數與其型別及說明。
2. **LLM 判斷：** LLM 接收使用者請求以及可用的工具定義。基於對請求與工具的理解，LLM 決定是否需要呼叫一個或多個工具來完成任務。
3. **函數呼叫產生：** 如果 LLM 決定使用工具，它會產生結構化輸出（通常是 JavaScript 物件標記法（JSON）物件），指出要呼叫的工具名稱以及從使用者請求中擷取的參數。
4. **工具執行：** 代理框架或協調層攔截這個結構化輸出，辨識指定的工具並以提供的參數執行實際的外部函數。
5. **觀察／結果：** 工具執行的輸出或結果會回傳給代理。
6. **LLM 處理（選用但常見）：** LLM 將工具輸出作為上下文，據此擬定最終回應或決定下一步工作流程（可能再次呼叫工具、進行反思，或提供最後答案）。

這個模式至關重要，因為它打破了 LLM 訓練資料的限制，讓模型能取得最新資訊、執行無法在內部完成的計算、存取使用者專屬資料，或觸發現實世界的動作。函數調用（Function Calling）是連結 LLM 推理能力與龐大外部功能之間差距的技術機制。

雖然「函數調用（Function Calling）」很貼切地描述了呼叫特定、預先定義程式碼函數的行為，但我們可以進一步從「工具調用（Tool Calling）」的廣義角度來思考。這個更廣的詞彙強調代理能力遠超過單純執行函數；「工具」可以是傳統函數，也可以是複雜的應用程式介面端點（API endpoint）、資料庫查詢，甚至是對另一個專門代理的指令。這種觀點讓我們可以想像更精密的系統，例如主要代理可能會把複雜的資料分析工作交給專屬的「分析代理」，或透過 API 查詢外部知識庫。從「工具調用」角度思考，更能捕捉代理作為多元數位資源及其他智能實體協調者的全部潛力。

LangChain、LangGraph 與 Google Agent Developer Kit（ADK）等框架為工具定義與整合提供強大支援，經常運用 Gemini 或 OpenAI 系列等現代 LLM 原生的函數調用能力。在這些框架的「畫布」上，你可以定義工具，然後設定代理（通常是 LLM 代理）認知並使用這些工具。

工具使用是打造強大、具互動性且具外部感知能力代理的基石模式。

## 實務應用與使用案例

工具使用模式適用於幾乎所有代理需要超越文字生成、執行動作或擷取特定動態資訊的情境：

### 1. 從外部來源擷取資訊

取得 LLM 訓練資料以外的即時數據或資訊。

* **使用案例：** 天氣代理。
  * **工具：** 需要位置並回傳當前天氣狀況的天氣應用程式介面（Weather API）。
  * **代理流程：** 使用者詢問：「倫敦的天氣如何？」LLM 辨識需要使用天氣工具，使用「London」呼叫工具，工具回傳資料，LLM 將資料整理成易讀回應。

### 2. 與資料庫及應用程式介面互動

對結構化資料進行查詢、更新或其他操作。

* **使用案例：** 電子商務代理。
  * **工具：** 用於查詢商品庫存、取得訂單狀態或處理付款的應用程式介面呼叫。
  * **代理流程：** 使用者詢問「產品 X 有庫存嗎？」LLM 呼叫庫存應用程式介面，工具回傳庫存數量，LLM 告知使用者庫存狀態。

### 3. 執行計算與資料分析

使用外部計算器、資料分析函式庫或統計工具。

* **使用案例：** 財務代理。
  * **工具：** 計算器函數、股市資料應用程式介面、試算表工具。
  * **代理流程：** 使用者詢問「AAPL 現價是多少？如果我以每股 150 美元買入 100 股，潛在利潤是多少？」LLM 呼叫股票應用程式介面取得現價，再呼叫計算器工具取得結果，整理成回應。

### 4. 發送通訊

透過外部通訊服務發送電郵、訊息或進行應用程式介面呼叫。

* **使用案例：** 個人助理代理。
  * **工具：** 發送電郵的應用程式介面。
  * **代理流程：** 使用者說：「幫我發電郵給 John 提醒明天開會。」LLM 使用從請求中擷取的收件者、主題和內容呼叫電郵工具。

### 5. 執行程式碼

在安全環境中執行程式碼片段來完成特定任務。

* **使用案例：** 程式協助代理。
  * **工具：** 程式碼解譯器（Code interpreter）。
  * **代理流程：** 使用者提供一段 Python 程式碼並詢問：「這段程式在做什麼？」LLM 使用解譯器工具執行程式碼並分析輸出。

### 6. 控制其他系統或裝置

與智能家居裝置、物聯網平台或其他連線系統互動。

* **使用案例：** 智能家居代理。
  * **工具：** 控制智能燈光的應用程式介面。
  * **代理流程：** 使用者說：「關掉客廳燈。」LLM 將指令與目標裝置交給智能家居工具執行。

工具使用讓語言模型從文字產生器變成能夠在數位或實體世界中感知、推理與行動的代理（見圖 1）。

![Some Examples of an Agent Using Tool](Some_Examples_of_an_Agent_Using_Tool.png)

圖 1：代理使用工具的部分示例

## 實作範例：LangChain

在 LangChain 框架中實現工具使用分成兩個階段。首先定義一個或多個工具，通常是封裝既有的 Python 函數或其他可執行元件。接著將這些工具綁定到語言模型，賦予模型在認為需要外部函數呼叫才能滿足使用者查詢時，生成結構化工具使用請求的能力。

下列實作會先定義一個模擬資訊擷取工具的簡單函數，再建立代理並設定其在接收使用者輸入時善用這個工具。執行這個範例需要安裝 LangChain 核心函式庫以及特定模型供應商套件。此外，也必須設定所選語言模型服務的憑證，通常透過在本機環境中設定應用程式介面金鑰（API key）。

```python
import os
import getpass
import asyncio
import nest_asyncio
from typing import List
from dotenv import load_dotenv
import logging

from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.tools import tool as langchain_tool
from langchain.agents import create_tool_calling_agent, AgentExecutor


# UNCOMMENT
# Prompt the user securely and set API keys as environment variables
os.environ["GOOGLE_API_KEY"] = getpass.getpass("Enter your Google API key: ")
os.environ["OPENAI_API_KEY"] = getpass.getpass("Enter your OpenAI API key: ")

try:
    # A model with function/tool calling capabilities is required.
    llm = ChatGoogleGenerativeAI(model="gemini-2.0-flash", temperature=0)
    print(f"✅ Language model initialized: {llm.model}")
except Exception as e:
    print(f"🛑 Error initializing language model: {e}")
    llm = None


# --- Define a Tool ---
@langchain_tool
def search_information(query: str) -> str:
    """
    Provides factual information on a given topic. Use this tool to find answers to phrases
    like 'capital of France' or 'weather in London?'.
    """
    print(f"\n--- 🛠️ Tool Called: search_information with query: '{query}' ---")

    # Simulate a search tool with a dictionary of predefined results.
    simulated_results = {
        "weather in london": "The weather in London is currently cloudy with a temperature of 15°C.",
        "capital of france": "The capital of France is Paris.",
        "population of earth": "The estimated population of Earth is around 8 billion people.",
        "tallest mountain": "Mount Everest is the tallest mountain above sea level.",
        "default": f"Simulated search result for '{query}': No specific information found, but the topic seems interesting.",
    }
    result = simulated_results.get(query.lower(), simulated_results["default"])
    print(f"--- TOOL RESULT: {result} ---")
    return result


tools = [search_information]


# --- Create a Tool-Calling Agent ---
if llm:
    # This prompt template requires an `agent_scratchpad` placeholder for the agent's internal steps.
    agent_prompt = ChatPromptTemplate.from_messages([
        ("system", "You are a helpful assistant."),
        ("human", "{input}"),
        ("placeholder", "{agent_scratchpad}"),
    ])

    # Create the agent, binding the LLM, tools, and prompt together.
    agent = create_tool_calling_agent(llm, tools, agent_prompt)

    # AgentExecutor is the runtime that invokes the agent and executes the chosen tools.
    # The 'tools' argument is not needed here as they are already bound to the agent.
    agent_executor = AgentExecutor(agent=agent, verbose=True, tools=tools)


async def run_agent_with_tool(query: str):
    """Invokes the agent executor with a query and prints the final response."""
    print(f"\n--- 🏃 Running Agent with Query: '{query}' ---")
    try:
        response = await agent_executor.ainvoke({"input": query})
        print("\n--- ✅ Final Agent Response ---")
        print(response["output"])
    except Exception as e:
        print(f"\n🛑 An error occurred during agent execution: {e}")


async def main():
    """Runs all agent queries concurrently."""
    tasks = [
        run_agent_with_tool("What is the capital of France?"),
        run_agent_with_tool("What's the weather like in London?"),
        run_agent_with_tool("Tell me something about dogs."),  # Should trigger the default tool response
    ]
    await asyncio.gather(*tasks)


nest_asyncio.apply()
asyncio.run(main())
```

這段程式碼使用 LangChain 函式庫與 Google Gemini 模型建立一個呼叫工具的代理。它定義了一個模擬提供特定查詢事實答案的 `search_information` 工具。該工具對「weather in london」、「capital of france」與「population of earth」等查詢提供預設回應，對其他查詢則提供通用回應。程式碼會初始化具備工具呼叫能力的 ChatGoogleGenerativeAI 模型，並建立 ChatPromptTemplate 來引導代理互動。`create_tool_calling_agent` 函數會把語言模型、工具與提示結合成代理，再透過 AgentExecutor 管理代理的執行與工具呼叫。非同步函數 `run_agent_with_tool` 用來呼叫代理並輸出結果，而主要的非同步函數則同時執行多個查詢，以測試工具的特定及預設回應。程式只在 LLM 成功初始化後才會繼續設定與執行代理。

## 實作範例：CrewAI

這段程式碼示範如何在 CrewAI 框架內實作函數調用（Tools），設定一個配備工具以查詢資訊的簡易情境。範例透過代理與工具模擬擷取股票價格。

```python
# pip install crewai langchain-openai

import os
from crewai import Agent, Task, Crew
from crewai.tools import tool
import logging


# --- Best Practice: Configure Logging ---
# A basic logging setup helps in debugging and tracking the crew's execution.
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')


# --- Set up your API Key ---
# For production, it's recommended to use a more secure method for key management
# like environment variables loaded at runtime or a secret manager.
#
# Set the environment variable for your chosen LLM provider (e.g., OPENAI_API_KEY)
# os.environ["OPENAI_API_KEY"] = "YOUR_API_KEY"
# os.environ["OPENAI_MODEL_NAME"] = "gpt-4o"


# --- 1. Refactored Tool: Returns Clean Data ---
# The tool now returns raw data (a float) or raises a standard Python error.
# This makes it more reusable and forces the agent to handle outcomes properly.
@tool("Stock Price Lookup Tool")
def get_stock_price(ticker: str) -> float:
    """
    Fetches the latest simulated stock price for a given stock ticker symbol.
    Returns the price as a float. Raises a ValueError if the ticker is not found.
    """
    logging.info(f"Tool Call: get_stock_price for ticker '{ticker}'")
    simulated_prices = {
        "AAPL": 178.15,
        "GOOGL": 1750.30,
        "MSFT": 425.50,
    }
    price = simulated_prices.get(ticker.upper())
    if price is not None:
        return price
    else:
        # Raising a specific error is better than returning a string.
        # The agent is equipped to handle exceptions and can decide on the next action.
        raise ValueError(f"Simulated price for ticker '{ticker.upper()}' not found.")


# --- 2. Define the Agent ---
# The agent definition remains the same, but it will now leverage the improved tool.
financial_analyst_agent = Agent(
    role='Senior Financial Analyst',
    goal='Analyze stock data using provided tools and report key prices.',
    backstory="You are an experienced financial analyst adept at using data sources to find stock information. You provide clear, direct answers.",
    verbose=True,
    tools=[get_stock_price],
    # Allowing delegation can be useful, but is not necessary for this simple task.
    allow_delegation=False,
)


# --- 3. Refined Task: Clearer Instructions and Error Handling ---
# The task description is more specific and guides the agent on how to react
# to both successful data retrieval and potential errors.
analyze_aapl_task = Task(
    description=(
        "What is the current simulated stock price for Apple (ticker: AAPL)? "
        "Use the 'Stock Price Lookup Tool' to find it. "
        "If the ticker is not found, you must report that you were unable to retrieve the price."
    ),
    expected_output=(
        "A single, clear sentence stating the simulated stock price for AAPL. "
        "For example: 'The simulated stock price for AAPL is $178.15.' "
        "If the price cannot be found, state that clearly."
    ),
    agent=financial_analyst_agent,
)


# --- 4. Formulate the Crew ---
# The crew orchestrates how the agent and task work together.
financial_crew = Crew(
    agents=[financial_analyst_agent],
    tasks=[analyze_aapl_task],
    verbose=True  # Set to False for less detailed logs in production
)


# --- 5. Run the Crew within a Main Execution Block ---
# Using a __name__ == "__main__": block is a standard Python best practice.
def main():
    """Main function to run the crew."""
    # Check for API key before starting to avoid runtime errors.
    if not os.environ.get("OPENAI_API_KEY"):
        print("ERROR: The OPENAI_API_KEY environment variable is not set.")
        print("Please set it before running the script.")
        return

    print("\n## Starting the Financial Crew...")
    print("---------------------------------")

    # The kickoff method starts the execution.
    result = financial_crew.kickoff()

    print("\n---------------------------------")
    print("## Crew execution finished.")
    print("\nFinal Result:\n", result)


if __name__ == "__main__":
    main()
```

這段程式碼示範如何使用 Crew.ai 函式庫模擬財務分析任務。它定義了自訂工具 `get_stock_price`，用以模擬查詢預先定義股票代碼的價格。該工具會對有效代碼回傳浮點數，對無效代碼則拋出 ValueError。接著建立名為 `financial_analyst_agent` 的 Crew.ai 代理，角色為資深財務分析師，並賦予其 `get_stock_price` 工具。任務 `analyze_aapl_task` 指示代理使用工具查詢 AAPL 的模擬股價，並在成功與失敗情況下各自提供清楚的指引。之後建立 Crew，包含上述代理與任務，並將詳細紀錄（verbose）啟用以便追蹤執行。主程式區塊於啟動前檢查 `OPENAI_API_KEY` 環境變數，透過 kickoff() 方法執行任務並輸出結果。此外，程式也設定了基本的日誌記錄，以利監控工具呼叫與代理行為。

## 實作範例：Google ADK

Google Agent Developer Kit（ADK）包含一組原生整合的工具，可直接納入代理能力之中。

**Google 搜尋：** 其中一個代表性元件是 Google 搜尋工具。此工具直接連接 Google 搜尋引擎，讓代理能執行網頁搜尋並擷取外部資訊。

```python
from google.adk.agents import Agent as ADKAgent
from google.adk.runners import Runner
from google.adk.sessions import InMemorySessionService
from google.adk.tools import google_search
from google.genai import types
import nest_asyncio
import asyncio


# Define variables required for Session setup and Agent execution
APP_NAME = "Google Search Agent"
USER_ID = "user1234"
SESSION_ID = "1234"


# Define Agent with access to search tool
root_agent = ADKAgent(
    name="basic_search_agent",
    model="gemini-2.0-flash-exp",
    description="Agent to answer questions using Google Search.",
    instruction="I can answer your questions by searching the internet. Just ask me anything!",
    tools=[google_search],  # Google Search is a pre-built tool to perform Google searches.
)


# Agent Interaction
async def call_agent(query: str):
    """
    Helper function to call the agent with a query.
    """
    # Session and Runner
    session_service = InMemorySessionService()
    await session_service.create_session(
        app_name=APP_NAME,
        user_id=USER_ID,
        session_id=SESSION_ID,
    )

    runner = Runner(agent=root_agent, app_name=APP_NAME, session_service=session_service)

    content = types.Content(role='user', parts=[types.Part(text=query)])
    events = runner.run(user_id=USER_ID, session_id=SESSION_ID, new_message=content)

    for event in events:
        if event.is_final_response() and event.content:
            # Safely extract text from the final response
            if hasattr(event.content, "text") and event.content.text:
                final_response = event.content.text
            elif event.content.parts:
                final_response = "".join(
                    part.text for part in event.content.parts if getattr(part, "text", None)
                )
            else:
                final_response = ""
            print("Agent Response:", final_response)


nest_asyncio.apply()
asyncio.run(call_agent("what's the latest ai news?"))
```

這段程式碼展示如何在 Python 版 Google ADK 中建立並使用一個基本代理，透過 Google 搜尋工具回答問題。程式先匯入 IPython、google.adk 與 google.genai 等必要模組，定義應用程式名稱、使用者 ID 與工作階段 ID，並建立名為 `basic_search_agent` 的代理，描述與指示皆說明其用途，並配置 Google 搜尋工具。系統使用 InMemorySessionService（詳見第 8 章）管理代理的工作階段，並建立 Runner 來串連代理與工作階段服務。輔助函數 `call_agent` 將使用者查詢格式化為 types.Content 物件並傳遞給 runner.run，該方法回傳一系列事件。程式迭代這些事件以找出最終回應，安全地擷取文字內容並輸出。最後，示範呼叫 `call_agent` 查詢「what's the latest ai news?」，呈現代理的實際運作。

**程式碼執行：** Google ADK 亦包含多種專門任務的整合元件，其中的 `built_in_code_execution` 工具為代理提供沙盒化的 Python 直譯器。模型得以撰寫並執行程式碼，以完成計算任務、操作資料結構與執行程序腳本。此能力對需要確定邏輯與精準計算、超出機率型語言生成範疇的問題尤其關鍵。

```python
import os
import getpass
import asyncio
import nest_asyncio
from typing import List
from dotenv import load_dotenv
import logging

from google.adk.agents import Agent as ADKAgent, LlmAgent
from google.adk.runners import Runner
from google.adk.sessions import InMemorySessionService
from google.adk.tools import google_search
from google.adk.code_executors import BuiltInCodeExecutor
from google.genai import types


# Define variables required for Session setup and Agent execution
APP_NAME = "calculator"
USER_ID = "user1234"
SESSION_ID = "session_code_exec_async"


# Agent Definition
code_agent = LlmAgent(
    name="calculator_agent",
    model="gemini-2.0-flash",
    code_executor=BuiltInCodeExecutor(),
    instruction="""You are a calculator agent.
    When given a mathematical expression, write and execute Python code to calculate the result.
    Return only the final numerical result as plain text, without markdown or code blocks.
    """,
    description="Executes Python code to perform calculations.",
)


# Agent Interaction (Async)
async def call_agent_async(query: str):
    # Session and Runner
    session_service = InMemorySessionService()
    await session_service.create_session(app_name=APP_NAME, user_id=USER_ID, session_id=SESSION_ID)

    runner = Runner(agent=code_agent, app_name=APP_NAME, session_service=session_service)

    content = types.Content(role='user', parts=[types.Part(text=query)])
    print(f"\n--- Running Query: {query} ---")

    try:
        # Use run_async
        async for event in runner.run_async(user_id=USER_ID, session_id=SESSION_ID, new_message=content):
            print(f"Event ID: {event.id}, Author: {event.author}")

            if event.content and event.content.parts and event.is_final_response():
                for part in event.content.parts:  # Iterate through all parts
                    if getattr(part, "executable_code", None):
                        # Access the actual code string via .code
                        print(f"  Debug: Agent generated code:\n```python\n{part.executable_code.code}\n```")
                    elif getattr(part, "code_execution_result", None):
                        # Access outcome and output correctly
                        print(
                            "  Debug: Code Execution Result: "
                            f"{part.code_execution_result.outcome} - Output:\n{part.code_execution_result.output}"
                        )
                    elif getattr(part, "text", None) and not part.text.isspace():
                        # Also print any text parts found in any event for debugging
                        print(f"  Text: '{part.text.strip()}'")

                # --- Check for final response AFTER specific parts ---
                text_parts = [part.text for part in event.content.parts if getattr(part, "text", None)]
                final_result = "".join(text_parts)
                print(f"==> Final Agent Response: {final_result}")

    except Exception as e:
        print(f"ERROR during agent run: {e}")

    print("-" * 30)


# Main async function to run the examples
async def main():
    await call_agent_async("Calculate the value of (5 + 7) * 3")
    await call_agent_async("What is 10 factorial?")


# Execute the main async function
try:
    nest_asyncio.apply()
    asyncio.run(main())
except RuntimeError as e:
    # Handle specific error when running asyncio.run in an already running loop (like Jupyter/Colab)
    if "cannot be called from a running event loop" in str(e):
        print("\nRunning in an existing event loop (like Colab/Jupyter).")
        print("Please run `await main()` in a notebook cell instead.")
        # If in an interactive environment like a notebook, you might need to run:
        # await main()
    else:
        raise e  # Re-raise other runtime errors

    print("Agent: ", end="", flush=True)
    try:
        # Construct the message content correctly
        content = types.Content(role='user', parts=[types.Part(text=query)])

        # Process events as they arrive from the asynchronous runner
        async for event in runner.run_async(
            user_id=USER_ID,
            session_id=SESSION_ID,
            new_message=content,
        ):
            # For token-by-token streaming of the response text
            if hasattr(event, "content_part_delta") and event.content_part_delta:
                print(event.content_part_delta.text, end="", flush=True)

            # Process the final response and its associated metadata
            if event.is_final_response():
                print()  # Newline after the streaming response
                if getattr(event, "grounding_metadata", None):
                    print(
                        f"  (Source Attributions: "
                        f"{len(event.grounding_metadata.grounding_attributions)} sources found)"
                    )
                else:
                    print("  (No grounding metadata found)")
                print("-" * 30)
    except Exception as e:
        print(f"\nAn error occurred: {e}")
        print("Please ensure your datastore ID is correct and that the service account has the necessary permissions.")
        print("-" * 30)


# --- Run Example ---
async def run_vsearch_example():
    # Replace with a question relevant to YOUR datastore content
    await call_vsearch_agent_async("Summarize the main points about the Q2 strategy document.")
    await call_vsearch_agent_async("What safety procedures are mentioned for lab X?")


# --- Execution ---
if __name__ == "__main__":
    if not DATASTORE_ID:
        print("Error: DATASTORE_ID environment variable is not set.")
    else:
        try:
            asyncio.run(run_vsearch_example())
        except RuntimeError as e:
            # This handles cases where asyncio.run is called in an environment
            # that already has a running event loop (like a Jupyter notebook).
            if "cannot be called from a running event loop" in str(e):
                print("Skipping execution in a running event loop. Please run this script directly.")
            else:
                raise e
```

這個腳本利用 Google 的代理開發工具包（Agent Development Kit, ADK）建立一個透過撰寫並執行 Python 程式碼解決數學問題的代理。它定義了一個被明確指示扮演計算機角色的 LlmAgent，並替它配置 `built_in_code_execution` 工具。核心邏輯集中在 `call_agent_async` 函式，該函式會把使用者查詢送到代理的執行器（Runner）並處理回傳的事件。在這個函式中，非同步迴圈逐一走訪事件，為除錯用途輸出代理產生的 Python 程式碼以及執行結果。程式特別區分這些中間步驟與包含最終數值答案的事件。最後，`main` 函式以兩個不同的數學運算示範代理的計算能力。

**企業搜尋（Enterprise Search）：** 這段程式碼使用 Python 版 google.adk 函式庫建構 Google ADK 應用，核心是 VSearchAgent。它設計成透過搜尋指定的 Vertex AI Search 資料儲存庫來回答問題。程式會初始化名為 `q2_strategy_vsearch_agent` 的 VSearchAgent，並提供描述、選用的模型（"gemini-2.0-flash-exp"）以及 Vertex AI Search 資料儲存庫的識別碼。`DATASTORE_ID` 預期以環境變數設定。接著建立代理的執行器（Runner），並以 InMemorySessionService 管理對話歷史。非同步函式 `call_vsearch_agent_async` 用於與代理互動：它接收查詢、組成訊息內容物件，並呼叫 runner 的 `run_async` 方法把查詢送給代理。函式會即時串流代理的回應到主控台，同時列出最終回應及任何來自資料儲存庫的來源註記。程式亦加入錯誤處理，以便在代理執行時偵測錯誤，提供例如資料儲存庫識別碼錯誤或權限不足等提示。另一個非同步函式 `run_vsearch_example` 展示如何以範例查詢呼叫代理。主要執行區塊會檢查 `DATASTORE_ID` 是否已設定，並透過 asyncio.run 執行範例，同時處理像 Jupyter Notebook 這類已在執行事件迴圈的環境。

```python
import asyncio
import os

from google.genai import types
from google.adk import agents
from google.adk.runners import Runner
from google.adk.sessions import InMemorySessionService


# --- Configuration ---
# Ensure you have set your GOOGLE_API_KEY and DATASTORE_ID environment variables
# For example:
# os.environ["GOOGLE_API_KEY"] = "YOUR_API_KEY"
# os.environ["DATASTORE_ID"] = "YOUR_DATASTORE_ID"
DATASTORE_ID = os.environ.get("DATASTORE_ID")


# --- Application Constants ---
APP_NAME = "vsearch_app"
USER_ID = "user_123"  # Example User ID
SESSION_ID = "session_456"  # Example Session ID


# --- Agent Definition (Updated with the newer model from the guide) ---
vsearch_agent = agents.VSearchAgent(
    name="q2_strategy_vsearch_agent",
    description="Answers questions about Q2 strategy documents using Vertex AI Search.",
    model="gemini-2.0-flash-exp",  # Updated model based on the guide's examples
    datastore_id=DATASTORE_ID,
    model_parameters={"temperature": 0.0},
)


# --- Runner and Session Initialization ---
runner = Runner(
    agent=vsearch_agent,
    app_name=APP_NAME,
    session_service=InMemorySessionService(),
)


# --- Agent Invocation Logic ---
async def call_vsearch_agent_async(query: str):
    """Initializes a session and streams the agent's response."""
    print(f"User: {query}")
    print("Agent: ", end="", flush=True)
    try:
        # Construct the message content correctly
        content = types.Content(role='user', parts=[types.Part(text=query)])

        # Process events as they arrive from the asynchronous runner
        async for event in runner.run_async(
            user_id=USER_ID,
            session_id=SESSION_ID,
            new_message=content,
        ):
            # For token-by-token streaming of the response text
            if hasattr(event, "content_part_delta") and event.content_part_delta:
                print(event.content_part_delta.text, end="", flush=True)

            # Process the final response and its associated metadata
            if event.is_final_response():
                print()  # Newline after the streaming response
                if getattr(event, "grounding_metadata", None):
                    print(
                        f"  (Source Attributions: "
                        f"{len(event.grounding_metadata.grounding_attributions)} sources found)"
                    )
                else:
                    print("  (No grounding metadata found)")
                print("-" * 30)
    except Exception as e:
        print(f"\nAn error occurred: {e}")
        print("Please ensure your datastore ID is correct and that the service account has the necessary permissions.")
        print("-" * 30)


# --- Run Example ---
async def run_vsearch_example():
    # Replace with a question relevant to YOUR datastore content
    await call_vsearch_agent_async("Summarize the main points about the Q2 strategy document.")
    await call_vsearch_agent_async("What safety procedures are mentioned for lab X?")


# --- Execution ---
if __name__ == "__main__":
    if not DATASTORE_ID:
        print("Error: DATASTORE_ID environment variable is not set.")
    else:
        try:
            asyncio.run(run_vsearch_example())
        except RuntimeError as e:
            # This handles cases where asyncio.run is called in an environment
            # that already has a running event loop (like a Jupyter notebook).
            if "cannot be called from a running event loop" in str(e):
                print("Skipping execution in a running event loop. Please run this script directly.")
            else:
                raise e
```

整體而言，這段程式碼提供建立會話式人工智慧應用的基礎框架，利用 Vertex AI Search 從資料儲存庫擷取資訊來回答問題。它示範如何定義代理、設定執行器（Runner），並在串流回應的同時以非同步方式與代理互動，重點在於整合特定資料儲存庫的資訊來回覆使用者查詢。

**Vertex 擴充功能（Vertex Extensions）：** Vertex AI 擴充功能是一種結構化的應用程式介面包裝器，讓模型可以連接外部 API 以處理即時資料並執行動作。擴充功能提供企業級的安全性、資料私隱與效能保證，可用於產生與執行程式碼、查詢網站以及分析私有資料儲存庫等任務。Google 提供多個常見使用案例的預建擴充功能，例如程式碼解譯器（Code Interpreter）與 Vertex AI Search，同時允許建立自訂擴充功能。其主要優勢在於強大的企業控管與與其他 Google 產品的無縫整合。擴充功能與函數調用之間的主要差異在於執行方式：Vertex AI 會自動執行擴充功能，而函數呼叫則需要使用者或客戶端手動執行。

## 重點速覽

**重點是什麼：** LLM 雖然是強大的文字生成器，但本質上與外部世界隔離。它們的知識是靜態的，受限於訓練資料，且缺乏執行動作或取得即時資訊的能力。沒有連接外部系統的橋樑，LLM 在解決實際問題時的效用會受到嚴重限制。

**為何重要：** 工具使用模式（Tool Use pattern）常透過函數調用（Function Calling）實作，為此問題提供標準化解決方案。系統會以 LLM 能理解的方式描述可用的外部函數或「工具」，代理型 LLM 便可根據使用者請求判斷是否需要工具，並生成結構化資料物件（例如 JSON），指定要呼叫的函數及其參數。協調層會執行該函數、擷取結果並回饋給 LLM，使其能把即時的外部資訊或動作結果納入最終回應，實際賦予其行動能力。

**經驗法則：** 只要代理需要突破 LLM 內部知識、與外界互動，就應採用工具使用模式。這對需要即時資料（例如查詢天氣、股價）、存取私有或專有資訊（例如查詢公司資料庫）、進行精準計算、執行程式碼或觸發其他系統動作（例如寄電郵、控制智能裝置）的任務至關重要。

**視覺摘要：**

![Tool Use Design Pattern](../assets/Tool_Use_Design_Pattern.png)

圖 2：工具使用設計模式

## 重要啟示

* 工具使用（Tool Use）／函數調用（Function Calling）讓代理能與外部系統互動並存取動態資訊。
* 這個模式涉及為 LLM 定義具體描述與參數的工具，以便模型理解。
* LLM 會決定何時使用工具並生成結構化的函數呼叫。
* 代理框架負責執行實際的工具呼叫並將結果回傳給 LLM。
* 工具使用是打造能執行現實世界動作並提供最新資訊代理的必要條件。
* LangChain 透過 @tool 裝飾器簡化工具定義，並提供 `create_tool_calling_agent` 與 AgentExecutor 來建立使用工具的代理。
* Google ADK 擁有多個非常實用的預建工具，例如 Google Search、Code Execution 與 Vertex AI Search Tool。

## 結論

工具使用模式是延伸大型語言模型固有文字生成能力、擴展功能範圍的關鍵架構原則。透過賦予模型與外部軟體與資料來源連接的能力，此範式讓代理得以執行動作、進行計算並從其他系統擷取資訊。這個流程涉及模型在判斷有必要滿足使用者請求時，生成結構化請求來呼叫外部工具。LangChain、Google ADK 與 Crew AI 等框架提供結構化抽象與元件，協助整合這些外部工具，並管理向模型曝光工具規格與解析其工具使用請求。如此能簡化開發能在外部數位環境中互動並採取行動的高階代理系統。

## References

1. LangChain Documentation (Tools): [https://python.langchain.com/docs/integrations/tools/](https://python.langchain.com/docs/integrations/tools/)
2. Google Agent Developer Kit (ADK) Documentation (Tools): [https://google.github.io/adk-docs/tools/](https://google.github.io/adk-docs/tools/)
3. OpenAI Function Calling Documentation: [https://platform.openai.com/docs/guides/function-calling](https://platform.openai.com/docs/guides/function-calling)
4. CrewAI Documentation (Tools): [https://docs.crewai.com/concepts/tools](https://docs.crewai.com/concepts/tools)
