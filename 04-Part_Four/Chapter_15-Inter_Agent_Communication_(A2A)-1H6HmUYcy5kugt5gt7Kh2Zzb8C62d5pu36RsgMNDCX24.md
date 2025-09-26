# 第15章：跨代理通訊（Inter-Agent Communication）（A2A）

單一人工智能代理即使具備進階能力，面對複雜且多面向的問題時仍常遇到限制。為了克服這些挑戰，跨代理通訊（Inter-Agent Communication）（A2A）讓採用不同框架建構的多樣化人工智能代理能夠有效協作。這類協作涵蓋流暢的協調、任務委派與資訊交換。

Google 的 A2A 通訊協定是為促進這種通用通訊而設計的開放標準。本章將探討 A2A 的核心概念、實際應用，以及其在 Google ADK（Agent Development Kit）中的實作方式。

## 跨代理通訊模式概覽

Agent2Agent（A2A）通訊協定是一項開放標準，旨在促成不同人工智能代理框架之間的通訊與協作。它確保互通性，使使用 LangGraph、CrewAI 或 Google ADK 等技術開發的代理能夠跨越原生差異進行合作。

A2A 獲得多家科技公司與服務供應商的支援，包括 Atlassian、Box、LangChain、MongoDB、Salesforce、SAP 及 ServiceNow。Microsoft 計劃將 A2A 整合至 Azure AI Foundry 與 Copilot Studio，以展現其對開放通訊協定的承諾。此外，Auth0 與 SAP 也正在將 A2A 支援整合至其平台與代理之中。

作為開源通訊協定，A2A 歡迎社群貢獻，以促進其演進與廣泛採用。

## A2A 的核心概念

A2A 通訊協定提供了一套結構化的代理互動方法，建立在多個核心概念之上。全面掌握這些概念，對於任何開發或整合符合 A2A 的系統都至關重要。A2A 的基礎支柱包含核心參與者、代理卡（Agent Card）、代理探索、通訊與任務、互動機制與安全性，以下將逐一深入說明。

**核心參與者：**A2A 涉及三個主要實體：

* 使用者：發起代理協助請求。
* A2A 用戶端（Client Agent）：代表使用者提出行動或資訊請求的應用程式或人工智能代理。
* A2A 伺服器（Remote Agent）：提供 HTTP 端點以處理用戶端請求並回傳結果的人工智能代理或系統。遠端代理以「黑箱」系統方式運作，意味著用戶端無須理解其內部運作細節。

**代理卡（Agent Card）：**代理的數位身分由其代理卡（Agent Card）定義，通常以 JSON 檔案呈現。此檔案包含用戶端互動與自動探索所需的重要資訊，包括代理的身分、端點 URL 與版本。它亦詳細描述支援的能力，例如串流或推送通知、特定技能、預設輸入／輸出模式與驗證需求。以下示例展示一個 WeatherBot 的代理卡（Agent Card）。

```json
{
    "name": "WeatherBot",
    "description": "Provides accurate weather forecasts and historical data.",
    "url": "http://weather-service.example.com/a2a",
    "version": "1.0.0",
    "capabilities": {
        "streaming": true,
        "pushNotifications": false,
        "stateTransitionHistory": true
    },
    "authentication": {
        "schemes": [
            "apiKey"
        ]
    },
    "defaultInputModes": [
        "text"
    ],
    "defaultOutputModes": [
        "text"
    ],
    "skills": [
        {
            "id": "get_current_weather",
            "name": "Get Current Weather",
            "description": "Retrieve real-time weather for any location.",
            "inputModes": [
                "text"
            ],
            "outputModes": [
                "text"
            ],
            "examples": [
                "What's the weather in Paris?",
                "Current conditions in Tokyo"
            ],
            "tags": [
                "weather",
                "current",
                "real-time"
            ]
        },
        {
            "id": "get_forecast",
            "name": "Get Forecast",
            "description": "Get 5-day weather predictions.",
            "inputModes": [
                "text"
            ],
            "outputModes": [
                "text"
            ],
            "examples": [
                "5-day forecast for New York",
                "Will it rain in London this weekend?"
            ],
            "tags": [
                "weather",
                "forecast",
                "prediction"
            ]
        }
    ]
}
```

**代理探索（Agent Discovery）：**此過程讓用戶端可以找出描述可用 A2A 伺服器能力的代理卡（Agent Card）。常見策略包括：

* 眾所周知的 URI（Well-Known URI）：代理會在標準化路徑（例如 /.well-known/agent.json）託管其代理卡（Agent Card）。此方法通常可為公開或特定網域的使用案例提供廣泛且多半自動的存取。
* 精選登錄冊（Curated Registries）：這些登錄冊提供集中式目錄，代理卡（Agent Card）會在此發布，並可依特定條件查詢。此方式適合需要集中管理與存取控制的企業環境。
* 直接設定（Direct Configuration）：代理卡（Agent Card）資訊會被嵌入或以私下方式分享。此方法適用於緊密耦合或私密系統，當中動態探索並非關鍵需求。

無論採用哪種方法，都必須保護代理卡（Agent Card）端點。若代理卡（Agent Card）包含敏感（雖非機密）資訊，可透過存取控制、雙向傳輸層安全性（mutual TLS，mTLS）或網路限制來強化安全。

**通訊與任務（Communications and Tasks）：**在 A2A 框架中，通訊以非同步任務為核心，任務代表代理之間所交換工作的形式。A2A 使用 HTTP 與 JSON-RPC 2.0 標準傳遞任務，支援多輪對話。

任務處理涵蓋多種互動模式：

* 同步請求-回應：用戶端提交請求並等待單一回應。若任務耗時較長，伺服器可先回傳「working」狀態與任務 ID。用戶端接著可執行其他工作，並定期透過新請求輪詢伺服器，以查詢任務是否已標記為「completed」或「failed」。
* 串流更新（Server-Sent Events，SSE）：適合接收即時的增量結果。此方法建立由伺服器至用戶端的一條持續單向連線，讓遠端代理能持續推送更新，例如狀態變更或部分結果，而無須用戶端多次發送請求。
* 推送通知（Webhooks）：專為執行時間極長或資源密集的任務而設計，避免維持連線或頻繁輪詢所造成的低效率。用戶端可註冊 webhook URL，伺服器會在任務狀態出現重大變更時（例如完成）向該 URL 發送非同步通知。

代理卡（Agent Card）會註明代理是否支援串流或推送通知能力。此外，A2A 與模態無關，意味著上述互動模式不僅適用於文字，也能處理音訊與視訊等其他資料型態，以支援豐富的多模態人工智能應用。串流與推送通知能力皆會在代理卡（Agent Card）中設定。

```json
# Synchronous Request Example
{
    "jsonrpc": "2.0",
    "id": "1",
    "method": "sendTask",
    "params": {
        "id": "task-001",
        "sessionId": "session-001",
        "message": {
            "role": "user",
            "parts": [
                {
                    "type": "text",
                    "text": "What is the exchange rate from USD to EUR?"
                }
            ]
        },
        "acceptedOutputModes": [
            "text/plain"
        ],
        "historyLength": 5
    }
}
```

此同步請求使用 `sendTask` 方法，用戶端提出查詢並期望收到單一且完整的答案。相對地，串流請求使用 `sendTaskSubscribe` 方法，以建立持續連線，讓代理能隨時間回傳多個增量更新或部分結果。

```json
# Streaming Request Example
{
    "jsonrpc": "2.0",
    "id": "2",
    "method": "sendTaskSubscribe",
    "params": {
        "id": "task-002",
        "sessionId": "session-001",
        "message": {
            "role": "user",
            "parts": [
                {
                    "type": "text",
                    "text": "What's the exchange rate for JPY to GBP today?"
                }
            ]
        },
        "acceptedOutputModes": [
            "text/plain"
        ],
        "historyLength": 5
    }
}
```

**安全性：**跨代理通訊（Inter-Agent Communication）（A2A）是系統架構中不可或缺的元素，透過多項內建機制確保代理之間安全無縫地交換資料，維護穩健性與完整性。

雙向傳輸層安全性（Mutual Transport Layer Security，TLS）會建立加密且具身份驗證的連線，以防止未經授權的存取與資料攔截，確保通訊安全。

完備的稽核日誌會精確記錄所有跨代理通訊，包含資料流、涉入代理與相關行動。這條稽核軌跡對於追溯、疑難排解與安全分析至關重要。

代理卡（Agent Card）會明確列出驗證要求，這份設定構件說明代理的身分、能力與安全政策，從而集中並簡化驗證管理。

憑證處理通常透過 OAuth 2.0 權杖或 API 金鑰等安全憑證進行，並以 HTTP 標頭傳遞。此方法可避免憑證在 URL 或訊息本文中曝光，提升整體安全性。

## A2A 與 MCP 的比較

A2A 是一項與 Anthropic 的模型上下文通訊協定（Model Context Protocol，MCP）互補的通訊協定（見圖 1）。MCP 聚焦於為代理與其與外部資料及工具的互動結構化上下文；相對地，A2A 促進代理之間的協調與通訊，讓它們能進行任務委派與協作。

![Comparison A2A and MCP Protocols](../assets/Comparison_A2A_and_MCP_Protocols.png)

圖 1：A2A 與 MCP 通訊協定比較

A2A 的目標是提升效率、降低整合成本，並在複雜的多代理人工智能系統開發中推動創新與互通。因此，深入理解 A2A 的核心組成與運作方式，是有效設計、實作與應用協作及互通人工智能代理系統的關鍵。

## 實務應用與使用案例

跨代理通訊對於在各種領域打造進階人工智能解決方案至關重要，可提升模組化、可擴充性與智能化程度。

* **多框架協作：**A2A 的主要使用案例是讓獨立的人工智能代理無論底層框架為何（例如 ADK、LangChain、CrewAI）都能通訊與協作。這對於建構由不同代理分別擅長處理特定問題面向的複雜多代理系統極為關鍵。
* **自動化工作流程協調：**在企業環境中，A2A 可讓代理透過委派與協調任務來實現複雜工作流程。例如，一個代理處理初始資料蒐集，然後委派另一代理進行分析，最後由第三個代理負責報告產製，全程透過 A2A 通訊協定互通資訊。
* **動態資訊擷取：**代理可以互相通訊以擷取並交換即時資訊。主要代理可向專門的「資料擷取代理」請求即時市場數據，後者透過外部 API 收集資訊再回傳。

## 實作範例

我們來檢視 A2A 通訊協定的實際應用。[https://github.com/google-a2a/a2a-samples/tree/main/samples](https://github.com/google-a2a/a2a-samples/tree/main/samples) 儲存庫提供 Java、Go 與 Python 範例，展示 LangGraph、CrewAI、Azure AI Foundry 與 AG2 等各種代理框架如何透過 A2A 互通。該儲存庫的所有程式碼皆採 Apache 2.0 授權。為進一步說明 A2A 的核心概念，我們將檢視使用 Google 驗證工具設定 A2A 伺服器的 ADK 代理程式碼片段。請參考 [https://github.com/google-a2a/a2a-samples/blob/main/samples/python/agents/birthday_planner_adk/calendar_agent/adk_agent.py](https://github.com/google-a2a/a2a-samples/blob/main/samples/python/agents/birthday_planner_adk/calendar_agent/adk_agent.py)。

```python
import datetime

from google.adk.agents import LlmAgent  # type: ignore[import-untyped]
from google.adk.tools.google_api_tool import CalendarToolset  # type: ignore[import-untyped]


async def create_agent(client_id: str, client_secret: str) -> LlmAgent:
    """Constructs the ADK agent."""
    toolset = CalendarToolset(client_id=client_id, client_secret=client_secret)
    return LlmAgent(
        model="gemini-2.0-flash-001",
        name="calendar_agent",
        description="An agent that can help manage a user's calendar",
        instruction=(
            f""" You are an agent that can help manage a user's calendar. Users will request information about the state of their calendar """
            f""" or to make changes to their calendar. Use the provided tools for interacting with the calendar API. """
            f""" If not specified, assume the calendar the user wants is the 'primary' calendar. """
            f""" When using the Calendar API tools, use well-formed RFC3339 timestamps. Today is {datetime.datetime.now()}. """
        ),
        tools=await toolset.get_tools(),
    )
```

上述 Python 程式碼定義了非同步函式 `create_agent`，用以建構 ADK LlmAgent。它先使用提供的用戶端認證初始化 `CalendarToolset`，以存取 Google Calendar API。接著建立一個 `LlmAgent` 實例，設定指定的 Gemini 模型、具描述性的名稱，以及管理使用者行事曆的指示。該代理透過 `CalendarToolset` 提供的行事曆工具來與 Calendar API 互動，從而回應使用者關於行事曆狀態或修改的查詢。代理指示會動態納入當前日期以提供時間背景。為示範代理的建構方式，我們將檢視 A2A 範例中 `calendar_agent` 的重要段落。

以下程式碼顯示代理如何定義其特定指示與工具。請注意，文中僅呈現說明此功能所需的程式碼；完整檔案可於 [https://github.com/a2aproject/a2a-samples/blob/main/samples/python/agents/birthday_planner_adk/calendar_agent/__main__.py](https://github.com/a2aproject/a2a-samples/blob/main/samples/python/agents/birthday_planner_adk/calendar_agent/__main__.py) 取得。

```python
def main(host: str = "0.0.0.0", port: int = 8000):
    # Verify an API key is set.
    # Not required if using Vertex AI APIs.
    if os.getenv("GOOGLE_GENAI_USE_VERTEXAI") != "TRUE" and not os.getenv("GOOGLE_API_KEY"):
        raise ValueError(
            "GOOGLE_API_KEY environment variable not set and "
            "GOOGLE_GENAI_USE_VERTEXAI is not TRUE."
        )

    skill = AgentSkill(
        id="check_availability",
        name="Check Availability",
        description="Checks a user's availability for a time using their Google Calendar",
        tags=["calendar"],
        examples=["Am I free from 10am to 11am tomorrow?"],
    )

    agent_card = AgentCard(
        name="Calendar Agent",
        description="An agent that can manage a user's calendar",
        url=f"http://{host}:{port}/",
        version="1.0.0",
        defaultInputModes=["text"],
        defaultOutputModes=["text"],
        capabilities=AgentCapabilities(streaming=True),
        skills=[skill],
    )

    adk_agent = asyncio.run(
        create_agent(
            client_id=os.getenv("GOOGLE_CLIENT_ID"),
            client_secret=os.getenv("GOOGLE_CLIENT_SECRET"),
        )
    )

    runner = Runner(
        app_name=agent_card.name,
        agent=adk_agent,
        artifact_service=InMemoryArtifactService(),
        session_service=InMemorySessionService(),
        memory_service=InMemoryMemoryService(),
    )
    agent_executor = ADKAgentExecutor(runner, agent_card)

    async def handle_auth(request: Request) -> PlainTextResponse:
        await agent_executor.on_auth_callback(
            str(request.query_params.get("state")),
            str(request.url),
        )
        return PlainTextResponse("Authentication successful.")

    request_handler = DefaultRequestHandler(
        agent_executor=agent_executor,
        task_store=InMemoryTaskStore(),
    )

    a2a_app = A2AStarletteApplication(
        agent_card=agent_card,
        http_handler=request_handler,
    )
    routes = a2a_app.routes()
    routes.append(
        Route(
            path="/authenticate",
            methods=["GET"],
            endpoint=handle_auth,
        )
    )
    app = Starlette(routes=routes)

    uvicorn.run(app, host=host, port=port)


if __name__ == "__main__":
    main()
```

此段 Python 程式碼示範如何設定一個符合 A2A 的「行事曆代理」，以 Google Calendar 檢查使用者的可用時段。流程包括驗證 API 金鑰或 Vertex AI 設定以處理驗證需求。代理的能力（包含「check_availability」技能）會在代理卡（Agent Card）中定義，並同時指定代理的網路位址。接著建立 ADK 代理，並為管理構件、工作階段與記憶體設定記憶體內服務。程式碼接續初始化 Starlette 網頁應用程式，整合驗證回呼與 A2A 通訊協定處理器，最後使用 Uvicorn 執行，以透過 HTTP 對外提供代理服務。

這些範例展示了構建符合 A2A 的代理流程，從定義其能力到將其作為網頁服務執行。透過使用代理卡（Agent Card）與 ADK，開發者能建立可互通的人工智能代理，並與 Google Calendar 等工具整合。此實務方法呈現了 A2A 在建立多代理生態系統時的應用。

建議進一步透過 [https://www.trickle.so/blog/how-to-build-google-a2a-project](https://www.trickle.so/blog/how-to-build-google-a2a-project) 所提供的示範程式碼深化對 A2A 的了解。該資源提供 Python 與 JavaScript 的 A2A 用戶端與伺服器範例、多代理網頁應用程式、命令列介面，以及多種代理框架的實作示例。

## 重點速覽

**內容重點：**單一人工智能代理，尤其是使用不同框架建構者，往往難以獨力應對複雜、多面向的問題。主要挑戰在於缺乏能讓它們有效通訊與協作的共同語言或通訊協定。這種孤立狀態阻礙了多個專精代理整合技能以解決更大型任務的可能。若沒有標準化方法，整合這些異質代理將耗費成本與時間，也阻礙更強大且一致的人工智能解決方案的開發。

**重要原因：**跨代理通訊（Inter-Agent Communication）（A2A）通訊協定以開放、標準化的方式解決上述問題。此 HTTP 型通訊協定促進互通性，讓不同的人工智能代理無論底層技術為何都能協調、委派任務並順暢交換資訊。核心組成之一是代理卡（Agent Card），這份數位身分檔案描述代理的能力、技能與通訊端點，有助於探索與互動。A2A 定義多種互動機制，包括同步與非同步通訊，以支援多元使用情境。透過為代理協作建立通用標準，A2A 能培養模組化且可擴充的多代理系統。

**實務守則：**當需要協調兩個或以上人工智能代理的協作時，特別是它們使用不同框架（例如 Google ADK、LangGraph、CrewAI）時，應使用此模式。此模式非常適合構建複雜且模組化的應用程式，讓專門代理處理工作流程的特定部分，例如將資料分析交由一個代理，再由另一代理負責報告生成。當代理需動態探索並使用其他代理的能力來完成任務時，此模式亦不可或缺。

**視覺摘要：**

![A2A Inter-Agent Communication Pattern](../assets/A2A_Inter-Agent_Communication_Pattern.png)

圖 2：A2A 跨代理通訊模式

## 重要啟示

重要啟示：

* Google A2A 通訊協定是開放的 HTTP 型標準，可促進使用不同框架建構的人工智能代理之間的通訊與協作。
* 代理卡（Agent Card）是代理的數位識別，讓其他代理能自動探索並理解其能力。
* A2A 提供同步請求-回應互動（使用 `tasks/send`）與串流更新（使用 `tasks/sendSubscribe`），以滿足不同通訊需求。
* 該通訊協定支援多輪對話，包括 `input-required` 狀態，讓代理能請求額外資訊並在互動中維持脈絡。
* A2A 鼓勵模組化架構，讓專門代理可以在不同埠口獨立運作，進而實現系統可擴充與分散部署。
* Trickle AI 等工具有助於視覺化並追蹤 A2A 通訊，協助開發者監控、除錯與最佳化多代理系統。
* 雖然 A2A 是針對不同代理之間任務與工作流程管理的高階通訊協定，但模型上下文通訊協定（Model Context Protocol，MCP）則提供大型語言模型與外部資源互動的標準化介面。

## 總結

跨代理通訊（Inter-Agent Communication）（A2A）通訊協定建立了一項關鍵的開放標準，藉此克服單一人工智能代理的孤立限制。透過提供通用的 HTTP 框架，它確保在 Google ADK、LangGraph 或 CrewAI 等不同平台上建構的代理能無縫協作與互通。核心組成是代理卡（Agent Card），作為數位身分，清楚定義代理能力並支援其他代理的動態探索。此通訊協定的彈性支援多元互動模式，包括同步請求、非同步輪詢與即時串流，以滿足廣泛的應用需求。

由此得以構建模組化且可擴充的架構，將專精代理組合起來協同處理複雜的自動化工作流程。安全性亦是基本要素，透過 mTLS 與明確的驗證要求等內建機制保護通訊。A2A 與 MCP 等其他標準互補，但其獨特焦點在於代理之間的高階協調與任務委派。大型科技公司的積極支持及實務實作的可得性，突顯了其日益重要的地位。此通訊協定為開發者打造更精密、分散且智能的多代理系統鋪路，是促進協作人工智能互通生態系的基石。

## References

1. Chen, B. (2025, April 22). *How to Build Your First Google A2A Project: A Step-by-Step Tutorial*. Trickle.so Blog. [https://www.trickle.so/blog/how-to-build-google-a2a-project](https://www.trickle.so/blog/how-to-build-google-a2a-project)
2. Google A2A GitHub Repository. [https://github.com/google-a2a/A2A](https://github.com/google-a2a/A2A)
3. Google Agent Development Kit (ADK) [https://google.github.io/adk-docs/](https://google.github.io/adk-docs/)
4. Getting Started with Agent-to-Agent (A2A) Protocol: [https://codelabs.developers.google.com/intro-a2a-purchasing-concierge#0](https://codelabs.developers.google.com/intro-a2a-purchasing-concierge#0)
5. Google AgentDiscovery - [https://a2a-protocol.org/latest/](https://a2a-protocol.org/latest/)
6. Communication between different AI frameworks such as LangGraph, CrewAI, and Google ADK [https://www.trickle.so/blog/how-to-build-google-a2a-project](https://www.trickle.so/blog/how-to-build-google-a2a-project#setting-up-your-a2a-development-environment)
7. Designing Collaborative Multi-Agent Systems with the A2A Protocol [https://www.oreilly.com/radar/designing-collaborative-multi-agent-systems-with-the-a2a-protocol/](https://www.oreilly.com/radar/designing-collaborative-multi-agent-systems-with-the-a2a-protocol/)
