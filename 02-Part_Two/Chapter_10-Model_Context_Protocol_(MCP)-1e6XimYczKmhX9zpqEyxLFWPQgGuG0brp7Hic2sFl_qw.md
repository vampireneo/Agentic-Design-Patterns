# 第十章：模型上下文協定（Model Context Protocol，MCP）

為了讓大型語言模型（Large Language Model，LLM）能有效地充當代理（agent），其能力必須超越多模態生成。它們需要與外部環境互動，包括存取最新資料、使用外部軟體以及執行特定營運任務。模型上下文協定（Model Context Protocol，MCP）透過提供標準化介面，讓 LLM 可以與外部資源互連，從而回應這個需求。此協定是促成一致且可預期整合的關鍵機制。

## MCP 模式概覽

想像有一個通用轉接器，任何 LLM 都能透過它插入任何外部系統、資料庫或工具，而不需要為每個系統客製整合。這正是模型上下文協定（Model Context Protocol，MCP）的概念。它是一套開放標準，旨在標準化 LLM（例如 Gemini、OpenAI 的 GPT 模型、Mixtral 和 Claude）與外部應用程式、資料來源和工具的互動方式。可以把它視為一種通用連接機制，簡化 LLM 取得脈絡、執行動作以及與不同系統互動的過程。

MCP 採用用戶端－伺服器（client-server）架構。它定義資料（稱為資源）、互動式範本（實際上就是提示（prompt））以及可執行函式（稱為工具（tool））如何由 MCP 伺服器公開。之後，MCP 用戶端——可能是 LLM 的託管應用程式或人工智慧代理（AI agent）本身——會使用這些元素。這種標準化做法大幅降低在多元營運環境中整合 LLM 的複雜度。

然而，MCP 是一份關於「代理介面」（agentic interface）的契約，其成效高度取決於所公開之底層應用程式介面（Application Programming Interface，API）的設計。開發者有可能只是把既有的傳統 API 包一層就算，這對代理而言並不理想。舉例來說，如果客訴系統的 API 只能逐一取得完整工單詳情，當代理被要求總結大量高優先度工單時就會既慢又不準。要真正發揮效益，底層 API 應加入篩選與排序等確定性（deterministic）功能，協助具非確定性（non-deterministic）的代理高效運作。這凸顯出代理並不能神奇地取代確定性工作流程，它們往往需要更強的確定性支援才會成功。

此外，MCP 亦可能包裝一個其輸入或輸出仍不易被代理理解的 API。若資料格式對代理不友善，API 就毫無用處，而 MCP 並不保證這一點。例如，若為文件儲存庫建立 MCP 伺服器卻只回傳 PDF 格式檔案，當代理無法解析 PDF 內容時就幾乎派不上用場。較佳作法是先建立一個能回傳文本版本（例如 Markdown）的 API，讓代理真正能讀取與處理。這顯示開發者必須考慮的不僅是連接本身，還有被交換資料的性質，以確保真正的相容性。

## MCP 與工具函式呼叫（tool function calling）

模型上下文協定（Model Context Protocol，MCP）與工具函式呼叫（tool function calling）是兩種不同機制，讓 LLM 能與外部能力（包括工具）互動並執行動作。雖然兩者都能讓 LLM 的能力超越文字生成，但在方法和抽象層級上有所不同。

工具函式呼叫可以視為 LLM 對某個特定且預先定義的工具或函式提出直接請求。在這個脈絡下，「工具」與「函式」視同一詞。這種互動屬於一對一溝通模式，LLM 會基於對使用者意圖的理解來格式化請求，以滿足需要外部動作的需求。應用程式程式碼隨後執行這項請求，並把結果回傳給 LLM。這個過程通常具備專屬性，且在不同 LLM 供應商之間差異甚大。

相對之下，模型上下文協定（Model Context Protocol，MCP）扮演標準化介面，讓 LLM 能探索、溝通並運用外部能力。它作為開放協定，促成 LLM 與各式工具及系統互動，目標是建立一個任何符合規範的工具都能被任何符合規範的 LLM 存取的生態系。這提升互操作性（interoperability）、可組合性（composability）與可重用性（reusability），涵蓋不同系統與實作。透過採用聯邦模型（federated model），我們大幅改善互操作性並釋放既有資產的價值。此策略只需為既有或傳統服務加上一層符合 MCP 的介面，即可把它們帶入現代生態系。這些服務仍能獨立運作，但如今可以由 LLM 編排協作，組合成新的應用程式與工作流程，帶來敏捷性與重用性，而毋須昂貴地重寫基礎系統。

以下整理 MCP 與工具函式呼叫之間的基本差異：

| 特性 | 工具函式呼叫（Tool Function Calling） | 模型上下文協定（Model Context Protocol，MCP） |
| ----- | ----- | ----- |
| **標準化（Standardization）** | 專屬且依供應商而異，不同 LLM 供應商的格式與實作各不相同。 | 開放且標準化的協定，促進不同 LLM 與工具之間的互操作性。 |
| **範圍（Scope）** | LLM 直接請求執行某個特定、預先定義的函式。 | 提供更廣泛的框架，規範 LLM 與外部工具如何彼此探索與溝通。 |
| **架構（Architecture）** | LLM 與應用程式工具處理邏輯之間的一對一互動。 | 採用用戶端－伺服器架構，讓由 LLM 驅動的應用程式（客戶端）可連接並使用各式 MCP 伺服器（工具）。 |
| **可探索性（Discovery）** | LLM 會在特定對話脈絡中明確得知可用工具。 | 支援動態探索可用工具。MCP 客戶端可以查詢伺服器了解其提供的能力。 |
| **可重用性（Reusability）** | 工具整合通常與特定應用與 LLM 緊密耦合。 | 促進建立可重用且獨立的「MCP 伺服器」，任何符合規範的應用程式都能存取。 |

把工具函式呼叫想像成替 AI 配備一組客製工具，例如特定扳手與螺絲批；這對於固定任務的工作坊很有效率。模型上下文協定（Model Context Protocol，MCP）則像建構一套通用且標準化的電源插座系統。本身並不提供工具，但讓任何合規工具都可以插入並運作，打造一個動態且持續擴展的工作坊。

總結而言，工具函式呼叫提供對少數特定函式的直接存取；而 MCP 則是標準化通訊框架，讓 LLM 能探索並運用大量外部資源。對於簡單應用，少數特定工具已足夠；但若是需要適應性的複雜互聯 AI 系統，就必須仰賴 MCP 這種通用標準。

## MCP 的其他考量

雖然 MCP 是強大的框架，但要全面評估其是否適用於特定情境，仍需考慮數個關鍵層面。以下逐一說明：

* **工具 vs. 資源 vs. 提示（Tool vs. Resource vs. Prompt）**：必須了解這些元件的角色。資源屬靜態資料（例如 PDF 檔案、資料庫紀錄）。工具是執行動作的函式（例如發送電郵、查詢 API）。提示（prompt）是指引 LLM 如何與資源或工具互動的範本，確保互動具結構且有效。
* **可探索性（Discoverability）**：MCP 的一大優勢是 MCP 客戶端可以動態查詢伺服器，以了解其提供的工具與資源。這種「即時」（just-in-time）探索機制對需要適應新能力而毋須重新部署的代理特別有力。
* **安全性（Security）**：透過任何協定公開工具與資料都需要強韌的安全措施。MCP 實作必須納入驗證（authentication）與授權（authorization），以控制哪些客戶端能連線到哪些伺服器，以及可執行哪些動作。
* **實作（Implementation）**：雖然 MCP 是開放標準，實作過程可能很複雜。不過供應商已開始簡化這個流程。例如某些模型供應商如 Anthropic 或 FastMCP 提供軟體開發套件（Software Development Kit，SDK），可抽象掉大量樣板程式碼，讓開發者較容易建立與連線 MCP 客戶端與伺服器。
* **錯誤處理（Error Handling）**：全面的錯誤處理策略至關重要。協定必須定義如何把錯誤（例如工具執行失敗、伺服器不可用、請求無效）回傳給 LLM，好讓它理解問題並可能改採其他方式。
* **本機 vs. 遠端伺服器（Local vs. Remote Server）**：MCP 伺服器可以部署在代理同一台機器上，或部署在遠端伺服器。若涉及敏感資料，本機伺服器可提供速度與安全性；而遠端伺服器架構則可在組織內共享並擴充常用工具的存取。
* **即時互動 vs. 批次處理（On-demand vs. Batch）**：MCP 支援即時互動工作階段，也支援大規模批次處理。選擇取決於應用情境：例如需要即時工具存取的即時對話代理，或是逐批處理紀錄的資料分析管線。
* **傳輸機制（Transportation Mechanism）**：協定也定義通訊所依賴的傳輸層。在本機互動中，它使用基於標準輸入／輸出（standard input/output）的 JSON 遠端程序呼叫（JSON-RPC），提供高效的程序間通訊。對遠端連線，則利用可串流超文字傳輸協定（Streamable Hypertext Transfer Protocol，Streamable HTTP）與伺服器推送事件（Server-Sent Events，SSE）等網頁友善協定，以維持持久且高效的用戶端－伺服器溝通。

模型上下文協定（Model Context Protocol，MCP）運用用戶端－伺服器模型來標準化資訊流。理解各元件之間的互動，是掌握 MCP 進階代理行為的關鍵：

1. **大型語言模型（Large Language Model，LLM）**：是核心智能，負責處理使用者請求、擬定計劃，並決定何時需要存取外部資訊或執行動作。
2. **MCP 客戶端（MCP Client）**：是一個包覆 LLM 的應用程式或封裝層。它充當中介，將 LLM 的意圖轉換成符合 MCP 標準的正式請求，並負責探索、連線與與 MCP 伺服器溝通。
3. **MCP 伺服器（MCP Server）**：是通往外部世界的閘道。它向獲授權的 MCP 客戶端公開一組工具、資源與提示。每個伺服器通常負責特定領域，例如連接公司內部資料庫，...
* **資料庫整合（Database Integration）：** MCP 讓 LLM 與代理能無縫存取並操作資料庫中的結構化資料。例如透過 MCP Toolbox for Databases，代理可以查詢 Google BigQuery 資料集以取得即時資訊、生成報表或更新紀錄，全程由自然語言指令驅動。
* **生成式媒體編排（Generative Media Orchestration）：** MCP 讓代理能整合進階生成式媒體服務。透過 MCP Tools for Genmedia Services，代理可以協調 Google 的 Imagen 產生影像、Veo 製作影片、Chirp 3 HD 產生真實語音，以及 Lyria 創作音樂，使 AI 應用得以動態創作內容。
* **外部 API 互動（External API Interaction）：** MCP 提供標準化方式，讓 LLM 能呼叫並接收任何外部 API 回應。這表示代理可以取得即時天氣資料、抓取股價、發送電郵或與客戶關係管理（Customer Relationship Management，CRM）系統互動，將能力遠遠延伸至語言模型之外。
* **推理導向資訊擷取（Reasoning-Based Information Extraction）：** MCP 利用 LLM 強大的推理能力，促成超越傳統搜尋與擷取系統的有效、依查詢而定的資訊擷取。代理可分析文本，並抽取直接回答使用者複雜問題的特定條文、圖表或敘述，而不只是回傳整份文件。
* **自訂工具開發（Custom Tool Development）：** 開發者可以打造自訂工具並透過 MCP 伺服器（例如使用 FastMCP）曝光。如此一來，專門的內部功能或專有系統無須直接修改 LLM，就能以標準化、易於使用的格式提供給 LLM 與其他代理。
* **標準化的 LLM 與應用程式溝通（Standardized LLM-to-Application Communication）：** MCP 確保 LLM 與其互動應用程式之間維持一致的溝通層，降低整合成本，促進不同 LLM 供應商與託管應用程式的互操作性，並簡化複雜代理系統的開發。
* **複雜工作流程編排（Complex Workflow Orchestration）：** 結合多個透過 MCP 暴露的工具與資料來源，代理得以編排高度複雜、多步驟的工作流程。例如代理可以從資料庫取得客戶資料、生成個人化行銷圖像、撰寫量身訂製的電郵，再將其寄出，全都透過不同 MCP 服務完成。
* **物聯網裝置控制（IoT Device Control）：** MCP 能促成人工智慧代理與物聯網（Internet of Things，IoT）裝置互動。代理可以透過 MCP 向智慧家電、工業感測器或機器人下達指令，達成自然語言控制與實體系統自動化。
* **金融服務自動化（Financial Services Automation）：** 在金融領域，MCP 可讓 LLM 與各種金融資料來源、交易平台或合規系統互動。代理可能分析市場資料、執行交易、提供個人化財務建議，或自動化法規報告，同時維持安全且標準化的溝通。

總而言之，模型上下文協定（Model Context Protocol，MCP）讓代理可以存取來自資料庫、API 與網路資源的即時資訊。它也讓代理能透過整合與處理多種來源的資料來執行寄送電郵、更新紀錄、控制裝置、執行複雜任務等動作。此外，MCP 亦支援 AI 應用的媒體生成工具。

## 與 ADK 的實作範例

本節說明如何連線至提供檔案系統操作的本機 MCP 伺服器，讓 ADK 代理與本機檔案系統互動。

### 使用 MCPToolset 建立代理

若要設定代理與檔案系統互動，必須建立 `agent.py` 檔案（例如位於 `./adk_agent_samples/mcp_agent/agent.py`）。`MCPToolset` 會在 `LlmAgent` 物件的 `tools` 清單中初始化。務必把 `args` 清單內的 `"/path/to/your/folder"` 替換成 MCP 伺服器能存取之本機目錄的絕對路徑。這個目錄會成為代理執行檔案系統操作的根目錄。

```python
import os

from google.adk.agents import LlmAgent
from google.adk.tools.mcp_tool.mcp_toolset import MCPToolset, StdioServerParameters


# Create a reliable absolute path to a folder named 'mcp_managed_files'
# within the same directory as this agent script.
# This ensures the agent works out-of-the-box for demonstration.
# For production, you would point this to a more persistent and secure location.
TARGET_FOLDER_PATH = os.path.join(
    os.path.dirname(os.path.abspath(__file__)),
    "mcp_managed_files",
)

# Ensure the target directory exists before the agent needs it.
os.makedirs(TARGET_FOLDER_PATH, exist_ok=True)

root_agent = LlmAgent(
    model="gemini-2.0-flash",
    name="filesystem_assistant_agent",
    instruction=(
        "Help the user manage their files. You can list files, read files, and write files. "
        f"You are operating in the following directory: {TARGET_FOLDER_PATH}"
    ),
    tools=[
        MCPToolset(
            connection_params=StdioServerParameters(
                command="npx",
                args=[
                    "-y",  # Argument for npx to auto-confirm install
                    "@modelcontextprotocol/server-filesystem",
                    # This MUST be an absolute path to a folder.
                    TARGET_FOLDER_PATH,
                ],
            ),
            # Optional: You can filter which tools from the MCP server are exposed.
            # For example, to only allow reading:
            # tool_filter=['list_directory', 'read_file']
        )
    ],
)
```

`npx`（Node Package Execute），隨同 npm（Node Package Manager）5.2.0 或以上版本提供，是一個可以直接從 npm 登錄冊執行 Node.js 套件的工具。這樣就毋須進行全域安裝。本質上，`npx` 充當 npm 套件執行器，並且常用於運行大量以 Node.js 套件形式發佈的社群 MCP 伺服器。

建立 `__init__.py` 檔案是必要步驟，以確保 agent.py 檔案會被 Agent Development Kit（ADK）視為可被探索的 Python 套件一部分。這個檔案應與 [agent.py](http://agent.py) 存放在同一個目錄。

```python
# ./adk_agent_samples/mcp_agent/__init__.py
from . import agent
```

當然亦有其他受支援的指令可使用。例如，可以如下方式連線至 python3：

```python
connection_params = StdioConnectionParams(
    server_params={
        "command": "python3",
        "args": ["./agent/mcp_server.py"],
        "env": {
            "SERVICE_ACCOUNT_PATH": SERVICE_ACCOUNT_PATH,
            "DRIVE_FOLDER_ID": DRIVE_FOLDER_ID,
        },
    }
)
```

在 Python 的語境之中，UVX 是指一個使用 uv 於暫時、隔離的 Python 環境內執行指令的命令列工具。基本上，它讓你毋須在系統或專案環境中安裝，也可以運行 Python 工具與套件。你可以透過 MCP 伺服器運行它。

```python
connection_params = StdioConnectionParams(
    server_params={
        "command": "uvx",
        "args": ["mcp-google-sheets@latest"],
        "env": {
            "SERVICE_ACCOUNT_PATH": SERVICE_ACCOUNT_PATH,
            "DRIVE_FOLDER_ID": DRIVE_FOLDER_ID,
        },
    }
)
```

建立 MCP 伺服器之後，下一步就是連接它。

## 透過 ADK Web 連接 MCP 伺服器

要開始的話，請執行「adk web」。在終端機內前往 mcp_agent 的上一層目錄（例如 adk_agent_samples），然後執行：

```python
cd ./adk_agent_samples # Or your equivalent parent directory
adk web
```

當 ADK Web 使用者介面在瀏覽器內載入後，請在代理選單中選取 `filesystem_assistant_agent`。接著可以嘗試以下提示（prompt）：

* 「展示這個資料夾的內容。」
* 「讀取 `sample.txt` 檔案。」（假設 `sample.txt` 位於 `TARGET_FOLDER_PATH`。）
* 「`another_file.md` 裡面有甚麼？」

## 使用 FastMCP 建立 MCP 伺服器

FastMCP 是一個高階的 Python 框架，用於簡化 MCP 伺服器的開發。它提供抽象層，讓開發者能夠聚焦核心邏輯，同時降低協定複雜度。

此函式庫讓開發者可以透過簡單的 Python 裝飾器（decorator）快速定義工具、資源與提示（prompt）。其一大優勢是自動產生結構定義（schema），能夠聰明地解讀 Python 函式簽章、型別提示與說明字串，從而構建人工智慧模型介面（AI model interface）所需的規格。這種自動化減少手動設定，亦降低人為錯誤。

除了建立基本工具之外，FastMCP 還支援伺服器組合與代理（proxying）等進階架構模式。這有助於以模組化方式開發複雜、多元組件系統，並讓現有服務無縫整合進人工智慧可存取的框架。同時，FastMCP 亦提供優化功能，以支援高效率、分散式與可擴充的人工智慧驅動應用。

## 使用 FastMCP 建立伺服器

為了說明，以下示範伺服器提供一個基本的「greet」工具。當伺服器啟動後，ADK 代理與其他 MCP 客戶端便可透過超文字傳輸協定（Hypertext Transfer Protocol，HTTP）與這個工具互動。

```python
# fastmcp_server.py
# This script demonstrates how to create a simple MCP server using FastMCP.
# It exposes a single tool that generates a greeting.
# 1. Make sure you have FastMCP installed:
# pip install fastmcp

from fastmcp import FastMCP, Client


# Initialize the FastMCP server.
mcp_server = FastMCP()


# Define a simple tool function.
# The `@mcp_server.tool` decorator registers this Python function as an MCP tool.
# The docstring becomes the tool's description for the LLM.
@mcp_server.tool
def greet(name: str) -> str:
    """
    Generates a personalized greeting.

    Args:
        name: The name of the person to greet.

    Returns:
        A greeting string.
    """
    return f"Hello, {name}! Nice to meet you."


# Or if you want to run it from the script:
if __name__ == "__main__":
    mcp_server.run(
        transport="http",
        host="127.0.0.1",
        port=8000,
    )
```

這段 Python 指令碼定義了一個名為 greet 的函式，接收人的名字並回傳個人化問候語。函式上方的 @tool() 裝飾器會自動將它註冊成人工智慧或其他程式可使用的工具。FastMCP 會利用函式的說明字串與型別提示，告訴代理這個工具的運作方式、所需輸入以及預期輸出。

當指令碼執行時，會啟動 FastMCP 伺服器，並於 localhost:8000 監聽請求，讓 greet 函式以網路服務形式提供。之後可以設定某個代理連接此伺服器，並在較大型的任務中使用 greet 工具產生問候語。伺服器會持續運作，直到手動停止為止。

## 使用 ADK 代理消費 FastMCP 伺服器

可以把 ADK 代理設定為 MCP 客戶端，以使用運行中的 FastMCP 伺服器。這需要配置 HttpServerParameters，並填入 FastMCP 伺服器的網絡位址，通常是 <http://localhost:8000>。

可以加入 `tool_filter` 參數，限制代理只使用伺服器提供的特定工具，例如「greet」。當使用者輸入「Greet John Doe」之類的要求時，代理內嵌的 LLM 會辨識出經由 MCP 可用的「greet」工具，並以「John Doe」作為參數呼叫它，再把伺服器回應傳回給使用者。這個流程展示了如何把透過 MCP 曝光的自訂工具與 ADK 代理整合。

要完成這個設定，需要建立代理檔案（例如位於 ./adk_agent_samples/fastmcp_client_agent/ 的 agent.py）。此檔案會建立一個 ADK 代理，並使用 HttpServerParameters 與運作中的 FastMCP 伺服器建立連線。

```python
# ./adk_agent_samples/fastmcp_client_agent/agent.py
import os

from google.adk.agents import LlmAgent
from google.adk.tools.mcp_tool.mcp_toolset import MCPToolset, HttpServerParameters


# Define the FastMCP server's address.
# Make sure your fastmcp_server.py (defined previously) is running on this port.
FASTMCP_SERVER_URL = "http://localhost:8000"

root_agent = LlmAgent(
    model="gemini-2.0-flash",  # Or your preferred model
    name="fastmcp_greeter_agent",
    instruction='You are a friendly assistant that can greet people by their name. Use the "greet" tool.',
    tools=[
        MCPToolset(
            connection_params=HttpServerParameters(
                url=FASTMCP_SERVER_URL,
            ),
            # Optional: Filter which tools from the MCP server are exposed
            # For this example, we're expecting only 'greet'
            tool_filter=["greet"],
        )
    ],
)
```

這個指令碼定義了一個名為 `fastmcp_greeter_agent` 的代理，採用 Gemini 語言模型。它獲得明確指示，要以友善助理身份為人打招呼。關鍵在於，程式碼為代理配備執行任務所需的工具。它設定 MCPToolset 連接至在 localhost:8000 執行的獨立伺服器，也就是上一個例子中的 FastMCP 伺服器。代理獲得對該伺服器上 greet 工具的存取權。總括來說，這段程式碼建立了系統的客戶端，打造一個明白目標是向人問候並確切知道要用哪個外部工具來達成目標的智能代理。

需要在 `fastmcp_client_agent` 目錄內建立 `__init__.py` 檔案，以確保該代理會被 Agent Development Kit（ADK）視為可被探索的 Python 套件。

首先，開啟一個新終端機並執行 `python fastmcp_server.py` 以啟動 FastMCP 伺服器。然後在終端機中前往 `fastmcp_client_agent` 的上一層目錄（例如 `adk_agent_samples`），並執行 `adk web`。當 ADK Web 使用者介面在瀏覽器載入後，從代理選單中選取 `fastmcp_greeter_agent`。接著可以輸入「Greet John Doe」之類的提示來測試。代理會使用你 FastMCP 伺服器上的 `greet` 工具生成回應。

## 一目了然

**What：** 為了有效地擔任代理（agent），大型語言模型（Large Language Model，LLM）必須突破純文字生成能力。它們需要與外部環境互動，以存取最新資料並使用外部軟體。若欠缺標準化的溝通方式，每次 LLM 與外部工具或資料來源的整合都會變成客製化、複雜且不可重複利用的工程。這種臨時做法限制擴充性，亦令構建複雜且互相連結的人工智慧（Artificial Intelligence，AI）系統變得困難而低效。

**Why：** 模型上下文協定（Model Context Protocol，MCP）作為 LLM 與外部系統之間的通用介面，提供標準化的解決方案。它建立一套開放且標準化的協定，定義如何發現與使用外部能力。透過用戶端－伺服器（client-server）模型運作，MCP 讓伺服器能向任何符合要求的客戶端公開工具、資料資源與互動式提示。由 LLM 驅動的應用程式擔任客戶端，能以可預測方式動態探索並使用現有資源。這種標準化方法促進可互操作與可重用元件的生態系，大大簡化複雜代理工作流程的開發。

**Rule of thumb：** 當你要構建複雜、可擴充或企業級的代理系統，且需要與多樣化且不斷演變的外部工具、資料來源與應用程式介面（Application Programming Interface，API）互動時，應採用模型上下文協定（Model Context Protocol，MCP）。當不同 LLM 與工具之間的互操作性是優先考量，並且代理需要在不重新部署的情況下動態探索新能力時，它尤其合適。對於較簡單且只需要固定、有限數量預先定義功能的應用，直接使用工具函式呼叫（tool function calling）可能已足夠。

**Visual summary：**

![Model Context Protocol](../assets/Model_Context_Protocol.png)

圖 1：模型上下文協定（Model Context Protocol）

## 重點提示

以下是重點整理：

* 模型上下文協定（Model Context Protocol，MCP）是一套開放標準，促成 LLM 與外部應用程式、資料來源及工具之間的標準化溝通。
* 它採用用戶端－伺服器（client-server）架構，定義公開與使用資源、提示（prompt）及工具的方法。
* Agent Development Kit（ADK）既支援使用現有 MCP 伺服器，也支援透過 MCP 伺服器曝光 ADK 工具。
* FastMCP 簡化 MCP 伺服器的開發與管理，特別適合曝光以 Python 實作的工具。
* MCP Tools for Genmedia Services 讓代理能整合 Google Cloud 的生成式媒體（generative media）能力（Imagen、Veo、Chirp 3 HD、Lyria）。
* MCP 讓 LLM 與代理能夠與現實世界系統互動，存取動態資訊，並執行超越文字生成的操作。

## 結論

模型上下文協定（Model Context Protocol，MCP）是一套協助大型語言模型（Large Language Model，LLM）與外部系統溝通的開放標準。它採用用戶端－伺服器架構，讓 LLM 能透過標準化工具存取資源、使用提示並執行操作。MCP 讓 LLM 可以與資料庫互動、管理生成式媒體流程、控制物聯網（Internet of Things，IoT）裝置，以及自動化金融服務。文中提供實務示例，示範如何設定代理與 MCP 伺服器通訊，包括檔案系統伺服器與以 FastMCP 建置的伺服器，並說明 MCP 與 Agent Development Kit（ADK）的整合。MCP 是發展具互動性的人工智慧代理（AI agent），使其能力超越基本語言處理的關鍵組件。

## References

1. Model Context Protocol (MCP) Documentation. (Latest). *Model Context Protocol (MCP)*. [https://google.github.io/adk-docs/mcp/](https://google.github.io/adk-docs/mcp/)
