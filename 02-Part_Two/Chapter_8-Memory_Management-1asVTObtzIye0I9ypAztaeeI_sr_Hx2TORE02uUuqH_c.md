# 第八章：記憶體管理（Memory Management）

有效的記憶體管理（memory management）對智能代理（intelligent agents）保留資訊至關重要。代理（agents）與人類相似，需要不同類型的記憶（memory）才能高效運作。本章深入探討記憶體管理，特別關注代理在即時（短期）（short-term）與持久（長期）（long-term）記憶需求上的處理。

在代理系統（agent systems）中，記憶（memory）指的是代理保留與運用過往互動、觀察與學習經驗資訊的能力。這項能力讓代理得以作出明智決策、維持對話脈絡並隨時間改善。代理記憶（agent memory）一般分為兩大類別：

* **短期記憶（語境記憶）（Short-Term Memory (Contextual Memory)）**：類似工作記憶，儲存目前正在處理或最近存取的資訊。對於使用大型語言模型（Large Language Models，LLMs）的代理而言，短期記憶主要存在於語境視窗（context window）內。這個視窗包含近期訊息、代理回覆、工具使用結果以及代理在當前互動中的反思，所有這些都會影響 LLM 隨後的回應與動作。語境視窗具有容量限制，制約代理可以直接存取的近期資訊量。高效的短期記憶管理涉及在這個有限空間內保留最相關的資訊，可能透過摘要較早的對話片段或強調關鍵細節的技巧來達成。具有「長語境」（long context）視窗的模型出現，僅僅是擴大了這種短期記憶的容量，讓單次互動中能保留更多資訊。然而，這類語境依舊是暫時的，一旦工作階段結束便會消失，而且每次處理所有內容既昂貴又低效。因此，代理需要額外的記憶類型，才能達成真正的持久性、從過往互動中提取資訊並建構持續的知識庫。
* **長期記憶（持久記憶）（Long-Term Memory (Persistent Memory)）**：扮演代理在多重互動、任務或較長時間範圍內需要保留資訊的儲存庫，類似長期知識庫。資料通常儲存在代理的即時處理環境之外，常見於資料庫、知識圖譜或向量資料庫（vector databases）中。在向量資料庫內，資訊會轉換成數值向量並加以儲存，使代理能夠根據語意相似度而非精確關鍵字匹配來檢索資料，這個過程稱為語意搜尋（semantic search）。當代理需要長期記憶中的資訊時，會查詢外部儲存體、擷取相關資料並將其整合進短期語境中立即使用，從而把既有知識與當前互動結合起來。

## 實務應用與使用情境（Practical Applications & Use Cases）

記憶體管理對代理追蹤資訊並在時間軸上展現智慧至關重要。這是代理超越基本問答能力的必要條件。應用情境包括：

* **聊天機器人與會話式人工智慧（Chatbots and Conversational AI）**：維持對話流程依賴短期記憶。聊天機器人需要記住先前使用者輸入，才能提供一致的回應。長期記憶讓聊天機器人能回想使用者偏好、過往問題或先前對話，提供個人化且持續的互動。
* **任務導向型代理（Task-Oriented Agents）**：負責管理多步驟任務的代理需要短期記憶來追蹤先前步驟、目前進度與整體目標。這些資訊可能存放在任務語境或暫存區中。長期記憶對於存取不在即時語境內的特定使用者資料至關重要。
* **個人化體驗（Personalized Experiences）**：提供客製化互動的代理會運用長期記憶儲存並擷取使用者偏好、過往行為與個人資訊，讓代理可以調整回應與建議。
* **學習與改進（Learning and Improvement）**：代理可以透過學習過往互動來優化表現。成功策略、錯誤與新資訊會儲存在長期記憶中，以利日後調整。強化學習代理（reinforcement learning agents）以此方式儲存已學得的策略或知識。
* **資訊檢索（檢索增強生成）（Information Retrieval (Retrieval Augmented Generation, RAG)）**：專為回答問題而設計的代理會存取知識庫，也就是它們的長期記憶，通常透過檢索增強生成（RAG）實作。代理會擷取相關文件或資料來支援回應。
* **自主系統（Autonomous Systems）**：機器人或自動駕駛車需要記憶地圖、路線、物件位置與學得的行為。這包含短期記憶用於處理即時環境，以及長期記憶用於一般環境知識。

記憶（memory）讓代理能維持歷史、學習、個人化互動並處理複雜且依賴時間的問題。

## 實作程式碼：Google Agent Developer Kit（ADK）中的記憶體管理（Hands-On Code: Memory Management in Google Agent Developer Kit (ADK)）

Google Agent Developer Kit（ADK）提供一套結構化方法來管理語境與記憶（memory），包含適用於實務的元件。扎實掌握 ADK 的 Session、State 與 Memory 對於構建需要保留資訊的代理至關重要。

就像人類互動一樣，代理需要回想先前交流才能進行連貫自然的對話。ADK 透過三個核心概念及其相關服務來簡化語境管理。

與代理的每次互動都可視為一條獨特的對話線索。代理可能需要存取較早互動的資料。ADK 的結構如下：

* **Session（工作階段）**：個別聊天線索，會記錄該次互動的訊息與動作（Events），同時儲存與該對話相關的暫存資料（State）。
* **State（`session.state`，狀態）**：儲存在 Session 內的資料，包含僅與目前活躍聊天線索相關的資訊。
* **Memory（記憶）**：可搜尋的資訊儲存庫，來源可能是過去多次聊天或外部來源，提供超出即時對話的資料檢索資源。

ADK 為管理構建複雜、具狀態與語境感知代理所需的關鍵元件提供專用服務。Session 服務（SessionService）管理聊天線索（Session 物件），負責啟動、記錄與結束；記憶服務（MemoryService）則監督長期知識（Memory）的儲存與擷取。

Session 服務（SessionService）與記憶服務（MemoryService）都提供多種設定選項，讓使用者能依應用需求選擇儲存方式。記憶體內（in-memory）選項適合測試用途，但資料不會在重啟後保留。為了持久儲存與擴充性，ADK 亦支援資料庫與雲端服務。

### Session：追蹤每一次聊天（Session: Keeping Track of Each Chat）

ADK 中的 Session 物件專為追蹤與管理個別聊天線索而設計。當與代理展開對話時，Session 服務（SessionService）會產生一個 `google.adk.sessions.Session` 物件。該物件封裝了特定對話線索的所有資料，包括唯一識別碼（`id`、`app_name`、`user_id`）、以 Event 物件呈現的時間順序記錄、稱為 state 的會話專屬暫存資料儲存區，以及顯示最後更新時間的時間戳記（`last_update_time`）。開發者通常透過 Session 服務（SessionService）間接與 Session 物件互動。Session 服務（SessionService）負責管理對話工作階段的生命週期，包括啟動新工作階段、恢復先前工作階段、記錄工作階段活動（包含狀態更新）、識別活躍工作階段並管理刪除工作階段資料。ADK 提供多種 Session 服務（SessionService）實作，採用不同儲存機制來保存工作階段歷史與暫存資料，例如 InMemorySessionService，適合測試但無法在應用重啟後保留資料。

```python
# Example: Using InMemorySessionService
# This is suitable for local development and testing where data
# persistence across application restarts is not required.
from google.adk.sessions import InMemorySessionService
session_service = InMemorySessionService()
```

此外，如果想將資料可靠地儲存到自行管理的資料庫，還可以使用 DatabaseSessionService。

```python
# Example: Using DatabaseSessionService
# This is suitable for production or development requiring persistent storage.
# You need to configure a database URL (e.g., for SQLite, PostgreSQL, etc.).
# Requires: pip install google-adk[sqlalchemy] and a database driver (e.g., psycopg2 for PostgreSQL)
from google.adk.sessions import DatabaseSessionService
# Example using a local SQLite file:
db_url = "sqlite:///./my_agent_data.db"
session_service = DatabaseSessionService(db_url=db_url)
```

此外，VertexAiSessionService 利用 Vertex AI 基礎設施，適用於 Google Cloud 上可擴充的生產環境。

```python
# Example: Using VertexAiSessionService
# This is suitable for scalable production on Google Cloud Platform, leveraging
# Vertex AI infrastructure for session management.
# Requires: pip install google-adk[vertexai] and GCP setup/authentication

from google.adk.sessions import VertexAiSessionService


PROJECT_ID = "your-gcp-project-id"  # Replace with your GCP project ID
LOCATION = "us-central1"  # Replace with your desired GCP location

# The app_name used with this service should correspond to the Reasoning Engine ID or name
REASONING_ENGINE_APP_NAME = (
    "projects/your-gcp-project-id/locations/us-central1/reasoningEngines/your-engine-id"
)  # Replace with your Reasoning Engine resource name

session_service = VertexAiSessionService(project=PROJECT_ID, location=LOCATION)

# When using this service, pass REASONING_ENGINE_APP_NAME to service methods:
# session_service.create_session(app_name=REASONING_ENGINE_APP_NAME, ...)
# session_service.get_session(app_name=REASONING_ENGINE_APP_NAME, ...)
# session_service.append_event(session, event, app_name=REASONING_ENGINE_APP_NAME)
# session_service.delete_session(app_name=REASONING_ENGINE_APP_NAME, ...)
```

選擇適合的 Session 服務（SessionService）至關重要，因為它決定代理互動歷史與暫存資料的儲存方式與持久性。

每次訊息交換都涉及循環流程：系統接收訊息，執行器（Runner）透過 Session 服務（SessionService） 取得或建立 Session，代理使用 Session 的語境（state 與歷史互動）處理訊息，代理產生回應並可能更新 state，執行器（Runner）將結果封裝為 Event，而 `session_service.append_event` 方法則在儲存中紀錄新事件並更新 state。Session 接著等待下一則訊息。理想情況下，互動結束時應使用 `delete_session` 方法終止工作階段。這個流程顯示 Session 服務（SessionService）如何透過管理 Session 專屬歷史與暫存資料來維持連續性。

### State：工作階段的草稿本（State: The Session's Scratchpad）

在 ADK 中，每個代表聊天線索的 Session 都包含 state 元件，類似代理在該次對話期間的臨時工作記憶。`session.events` 會記錄整個聊天歷史，而 `session.state` 則儲存並更新與活躍對話相關的動態資料。

根本上，`session.state` 以字典形式運作，透過鍵值配對存放資料。其核心功能是讓代理保留並管理對於連貫對話至關重要的細節，例如使用者偏好、任務進度、逐步累積的資料或影響代理後續行動的條件旗標。

state 結構由字串鍵與可序列化的 Python 型別值組成，包括字串、數字、布林值、清單以及包含這些基本型別的字典。state 是動態的，會在對話期間持續演變，其變更是否長存取決於所設定的 Session 服務（SessionService）。

可以利用鍵前綴來規劃 state，界定資料範圍與持久性。沒有前綴的鍵為工作階段專屬：

* `user:` 前綴會將資料與使用者 ID 連結，跨所有工作階段共用。
* `app:` 前綴指定應用程式所有使用者共享的資料。
* `temp:` 前綴代表僅在當前處理輪次有效且不會永久儲存的資料。

代理會透過單一的 `session.state` 字典存取所有 state 資料。Session 服務（SessionService）會處理資料擷取、合併與持久化。應在透過 `session_service.append_event()` 將 Event 加入工作階段歷史時更新 state，確保追蹤精確、在持久性服務中正確保存並安全處理 state 變更。

#### 1. 簡易方式：使用 `output_key`（適用於代理文字回覆）（The Simple Way: Using `output_key` (for Agent Text Replies)）

若只想把代理的最終文字回覆直接保存到 state，這是最簡單的方法。設定 LlmAgent 時，只需指定想使用的 `output_key`。執行器（Runner）會偵測到並在附加事件時自動建立必要的動作，把回應保存到 state。以下程式碼示範透過 `output_key` 更新 state：

```python
# Import necessary classes from the Google Agent Developer Kit (ADK)
from google.adk.agents import LlmAgent
from google.adk.sessions import InMemorySessionService, Session
from google.adk.runners import Runner
from google.genai.types import Content, Part


# Define an LlmAgent with an output_key.
greeting_agent = LlmAgent(
 name="Greeter",
 model="gemini-2.0-flash",
 instruction="Generate a short, friendly greeting.",
 output_key="last_greeting",
)


# --- Setup Runner and Session ---
app_name, user_id, session_id = "state_app", "user1", "session1"

session_service = InMemorySessionService()

runner = Runner(
    agent=greeting_agent,
    app_name=app_name,
    session_service=session_service,
)

session = session_service.create_session(
    app_name=app_name,
    user_id=user_id,
    session_id=session_id,
)

print(f"Initial state: {session.state}")


# --- Run the Agent ---
user_message = Content(parts=[Part(text="Hello")])

print("\n--- Running the agent ---")
for event in runner.run(
    user_id=user_id,
    session_id=session_id,
    new_message=user_message,
):
    if event.is_final_response():
        print("Agent responded.")


# --- Check Updated State ---
# Correctly check the state after the runner has finished processing all events.
updated_session = session_service.get_session(app_name, user_id, session_id)
print(f"\nState after agent run: {updated_session.state}")
```

在背後，執行器（Runner）會偵測你的 `output_key`，並在呼叫 `append_event` 時自動建立包含 `state_delta` 的必要動作。

#### 2. 標準方式：使用 `EventActions.state_delta`（適用於更複雜的更新）（The Standard Way: Using `EventActions.state_delta` (for More Complicated Updates)）

當你需要處理較複雜的情境——例如同時更新多個鍵、儲存非文字內容、鎖定 `user:` 或 `app:` 這類特定範圍，或進行與代理最終文字回覆無關的更新——就需要自行建立 state 變更字典（`state_delta`），並把它包含在你要附加的 Event 的 EventActions 中。以下為示例：

```python
import time

from google.adk.tools.tool_context import ToolContext
from google.adk.sessions import InMemorySessionService


# --- Define the Recommended Tool-Based Approach ---
def log_user_login(tool_context: ToolContext) -> dict:
    """
    Updates the session state upon a user login event.
    This tool encapsulates all state changes related to a user login.

    Args:
        tool_context: Automatically provided by ADK, gives access to session state.

    Returns:
        A dictionary confirming the action was successful.
    """
    # Access the state directly through the provided context.
    state = tool_context.state

    # Get current values or defaults, then update the state.
    # This is much cleaner and co-locates the logic.
    login_count = state.get("user:login_count", 0) + 1
    state["user:login_count"] = login_count
    state["task_status"] = "active"
    state["user:last_login_ts"] = time.time()
    state["temp:validation_needed"] = True

    print("State updated from within the `log_user_login` tool.")

    return {
        "status": "success",
        "message": f"User login tracked. Total logins: {login_count}.",
    }


# --- Demonstration of Usage ---
# In a real application, an LLM Agent would decide to call this tool.
# Here, we simulate a direct call for demonstration purposes.

# 1. Setup
session_service = InMemorySessionService()
app_name, user_id, session_id = "state_app_tool", "user3", "session3"

session = session_service.create_session(
    app_name=app_name,
    user_id=user_id,
    session_id=session_id,
    state={"user:login_count": 0, "task_status": "idle"},
)

print(f"Initial state: {session.state}")

# 2. Simulate a tool call (in a real app, the ADK Runner does this)
# We create a ToolContext manually just for this standalone example.
from google.adk.tools.tool_context import InvocationContext

mock_context = ToolContext(
    invocation_context=InvocationContext(
        app_name=app_name,
        user_id=user_id,
        session_id=session_id,
        session=session,
        session_service=session_service,
    )
)

# 3. Execute the tool
log_user_login(mock_context)

# 4. Check the updated state
updated_session = session_service.get_session(app_name, user_id, session_id)
print(f"State after tool execution: {updated_session.state}")

# Expected output will show the same state change as the "Before" case,
# but the code organization is significantly cleaner and more robust.
```

這段程式碼示範以工具為基礎的方式管理應用程式中的使用者工作階段 state。它定義函式 `log_user_login` 作為工具，負責在使用者登入事件時更新工作階段 state。
函式接收 ADK 提供的 ToolContext 物件，以存取並修改工作階段的 state 字典。工具內部會遞增 `user:login_count`、將 `task_status` 設為 "active"、紀錄 `user:last_login_ts`（timestamp），並加入暫時旗標 `temp:validation_needed`。

程式碼示範部分模擬此工具的使用方式。它設定記憶體內工作階段服務並建立具有預設 state 的初始工作階段。接著手動建立 ToolContext，以模擬 ADK 執行器（Runner）執行工具的環境，並呼叫 `log_user_login`。最後再次擷取 Session，顯示 state 已被工具更新。目的在於展示將 state 變更封裝在工具內如何讓程式碼更精簡且結構更佳，相較於在工具外直接操作 state。

請注意，直接在擷取 Session 後修改 `session.state` 字典並不建議，因為這會繞過標準事件處理機制。這類直接變更不會被記錄在工作階段的事件歷史中，可能無法由所選的 `SessionService`（Session 服務）持久化，可能導致併發問題，且無法更新時間戳記等重要中介資料。建議的做法是，在 `LlmAgent`（特別是代理最終文字回覆）上使用 `output_key` 參數，或在透過 `session_service.append_event()` 附加事件時，把 state 變更放進 `EventActions.state_delta`。`session.state` 應主要用於讀取既有資料。

總結來說，設計 state 時保持簡潔、使用基本資料型別、為鍵賦予清晰名稱並正確使用前綴，避免過度巢狀，且務必透過 append_event 流程更新 state。

## 記憶（Memory）：結合 MemoryService 的長期知識（Memory: Long-Term Knowledge with MemoryService）

在代理系統中，Session 元件會維持單一對話的聊天歷史（events）與暫存資料（state）。然而，若代理要在多次互動間保留資訊或存取外部資料，就需要長期知識管理，這由記憶服務（MemoryService）促成。

```python
# Example: Using InMemoryMemoryService
# This is suitable for local development and testing where data
# persistence across application restarts is not required.
# Memory content is lost when the app stops.

from google.adk.memory import InMemoryMemoryService

memory_service = InMemoryMemoryService()
```

可以把 Session 與 State 視為單一聊天工作階段的短期記憶，而由記憶服務（MemoryService）管理的長期知識則是持久且可搜尋的儲存庫。這個儲存庫可能包含多次過往互動或外部來源的資訊。BaseMemoryService 介面定義了管理這種可搜尋長期知識的標準。其主要功能包括新增資訊（從工作階段擷取內容並透過 `add_session_to_memory` 儲存）以及擷取資訊（讓代理查詢儲存庫並透過 `search_memory` 取得相關資料）。

ADK 提供多種實作來建立這個長期知識庫。InMemoryMemoryService 提供暫時儲存方案，適用於測試用途，但在應用重啟後資料不會保留。在生產環境中通常會使用 VertexAiRagMemoryService。此服務利用 Google Cloud 的檢索增強生成（Retrieval Augmented Generation，RAG）服務，提供可擴充、持久且具語意搜尋能力的解決方案（另請參閱第十四章的 RAG）。

```python
# Example: Using VertexAiRagMemoryService
# This is suitable for scalable production on GCP, leveraging
# Vertex AI RAG (Retrieval Augmented Generation) for persistent,
# searchable memory.
# Requires: pip install google-adk[vertexai], GCP
# setup/authentication, and a Vertex AI RAG Corpus.

from google.adk.memory import VertexAiRagMemoryService


# The resource name of your Vertex AI RAG Corpus
RAG_CORPUS_RESOURCE_NAME = (
    "projects/your-gcp-project-id/locations/us-central1/ragCorpora/your-corpus-id"
)  # Replace with your Corpus resource name

# Optional configuration for retrieval behavior
SIMILARITY_TOP_K = 5  # Number of top results to retrieve
VECTOR_DISTANCE_THRESHOLD = 0.7  # Threshold for vector similarity

memory_service = VertexAiRagMemoryService(
    rag_corpus=RAG_CORPUS_RESOURCE_NAME,
    similarity_top_k=SIMILARITY_TOP_K,
    vector_distance_threshold=VECTOR_DISTANCE_THRESHOLD,
)

# When using this service, methods like add_session_to_memory
# and search_memory will interact with the specified Vertex AI
# RAG Corpus.
```

## 實作程式碼：LangChain 與 LangGraph 的記憶體管理（Hands-on code: Memory Management in LangChain and LangGraph）

在 LangChain 與 LangGraph 中，記憶（Memory）是打造智慧且具自然互動感對話應用程式的關鍵元件。它讓人工智慧代理能記住過往互動中的資訊、從回饋中學習並調整以符合使用者偏好。LangChain 的記憶功能透過參照儲存歷史來加強當前提示，然後記錄最新互動供未來使用。隨著代理處理更複雜的任務，這項能力對效率與使用者滿意度愈發重要。

**短期記憶（Short-Term Memory）**：以工作執行緒為範圍，追蹤單一 Session 或執行緒內持續進行的對話。它提供即時語境，但完整歷史可能對 LLM 的語境視窗造成負擔，導致錯誤或效能不佳。LangGraph 將短期記憶管理為代理 state 的一部分，透過 checkpoint 機制持久化，使執行緒可隨時恢復。

**長期記憶（Long-Term Memory）**：儲存跨 Session 的使用者專屬或應用層級資料，並在對話執行緒間共用。它保存在自訂「命名空間」（namespaces）中，可在任何執行緒隨時取回。LangGraph 提供儲存機制以保存與召回長期記憶，讓代理能無限期保留知識。

LangChain 提供多種工具管理對話歷史，從手動控制到自動整合於 chain 之中。

**ChatMessageHistory：手動記憶管理（ChatMessageHistory: Manual Memory Management）**。若想在正式 chain 之外直接且簡易地掌控對話歷史，ChatMessageHistory 類別是理想選擇，可手動追蹤對話交換。

```python
from langchain.memory import ChatMessageHistory


# Initialize the history object
history = ChatMessageHistory()

# Add user and AI messages
history.add_user_message("I'm heading to New York next week.")
history.add_ai_message("Great! It's a fantastic city.")

# Access the list of messages
print(history.messages)
```

**ConversationBufferMemory：chain 的自動記憶（ConversationBufferMemory: Automated Memory for Chains）**。若要把記憶直接整合進 chains，ConversationBufferMemory 是常見選擇。它保存對話緩衝並將其提供給提示，可透過兩個關鍵參數調整行為：

* `memory_key`：指定在提示中存放聊天歷史的變數名稱，預設為 "history"。
* `return_messages`：布林值，決定歷史的格式。
  * 若為 `False`（預設），會回傳單一格式化字串，適合標準 LLM。
  * 若為 `True`，會回傳訊息物件清單，為聊天模型（Chat Models）建議的格式。

```python
from langchain.memory import ConversationBufferMemory


# Initialize memory
memory = ConversationBufferMemory()

# Save a conversation turn
memory.save_context(
    {"input": "What's the weather like?"},
    {"output": "It's sunny today."},
)

# Load the memory as a string
print(memory.load_memory_variables({}))
```

將這類記憶整合進 LLMChain，可讓模型存取對話歷史並提供語境相關的回應。

```python
from langchain_openai import OpenAI
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate
from langchain.memory import ConversationBufferMemory


# 1. Define LLM and Prompt
llm = OpenAI(temperature=0)

template = """You are a helpful travel agent.
Previous conversation: {history}
New question: {question}
Response:"""
prompt = PromptTemplate.from_template(template)

# 2. Configure Memory
# The memory_key "history" matches the variable in the prompt
memory = ConversationBufferMemory(memory_key="history")

# 3. Build the Chain
conversation = LLMChain(llm=llm, prompt=prompt, memory=memory)

# 4. Run the Conversation
response = conversation.predict(question="I want to book a flight.")
print(response)

response = conversation.predict(question="My name is Sam, by the way.")
print(response)

response = conversation.predict(question="What was my name again?")
print(response)
```

為了在聊天模型上獲得更佳效果，建議將 `return_messages=True`，以結構化訊息物件清單形式使用。

```python
from langchain_openai import ChatOpenAI
from langchain.chains import LLMChain
from langchain.memory import ConversationBufferMemory
from langchain_core.prompts import (
    ChatPromptTemplate,
    MessagesPlaceholder,
    SystemMessagePromptTemplate,
    HumanMessagePromptTemplate,
)


# 1. Define Chat Model and Prompt
llm = ChatOpenAI()

prompt = ChatPromptTemplate(
    messages=[
        SystemMessagePromptTemplate.from_template("You are a friendly assistant."),
        MessagesPlaceholder(variable_name="chat_history"),
        HumanMessagePromptTemplate.from_template("{question}"),
    ]
)

# 2. Configure Memory
# return_messages=True is essential for chat models
memory = ConversationBufferMemory(memory_key="chat_history", return_messages=True)

# 3. Build the Chain
conversation = LLMChain(llm=llm, prompt=prompt, memory=memory)

# 4. Run the Conversation
response = conversation.predict(question="Hi, I'm Jane.")
print(response)

response = conversation.predict(question="Do you remember my name?")
print(response)
```

**長期記憶的類型（Types of Long-Term Memory）**：長期記憶讓系統能在不同對話中保留資訊，提供更深入的語境與個人化體驗。它可比照人類記憶分成三種類型：

* **語意記憶：記住事實（Semantic Memory: Remembering Facts）**：用於保留特定事實與概念，例如使用者偏好或領域知識。這些資訊可作為代理回應的根基，讓互動更個人化且相關。這類資訊可以持續更新成使用者「檔案」（profile，JSON 文件），或整理成個別事實文件的「集合」（collection）。
* **情節記憶：記住經驗（Episodic Memory: Remembering Experiences）**：用於回想過往事件或行動。對人工智慧代理而言，情節記憶常被用來記住如何完成任務。實務上經常透過少樣本提示（few-shot example prompting）實作，代理會從過往成功的互動序列中學習，以正確完成任務。
* **程序記憶：記住規則（Procedural Memory: Remembering Rules）**：用於記住如何執行任務，也就是代理的核心指令與行為，通常包含在系統提示中。代理常會調整自身提示以適應與改進。「反思」（Reflection）是有效技巧，代理會檢視目前指令與近期互動，然後修訂自己的指令。

以下偽程式碼示範代理如何透過反思更新儲存在 LangGraph BaseStore 中的程序記憶：

```python
# Node that updates the agent's instructions
def update_instructions(state: State, store: BaseStore):
    namespace = ("instructions",)

    # Get the current instructions from the store
    current_instructions = store.search(namespace)[0]

    # Create a prompt to ask the LLM to reflect on the conversation
    # and generate new, improved instructions
    prompt = prompt_template.format(
        instructions=current_instructions.value["instructions"],
        conversation=state["messages"],
    )

    # Get the new instructions from the LLM
    output = llm.invoke(prompt)
    new_instructions = output["new_instructions"]

    # Save the updated instructions back to the store
    store.put(("agent_instructions",), "agent_a", {"instructions": new_instructions})


# Node that uses the instructions to generate a response
def call_model(state: State, store: BaseStore):
    namespace = ("agent_instructions",)

    # Retrieve the latest instructions from the store
    instructions = store.get(namespace, key="agent_a")[0]

    # Use the retrieved instructions to format the prompt
    prompt = prompt_template.format(
        instructions=instructions.value["instructions"]
    )
    # ... application logic continues
```

LangGraph 會將長期記憶儲存在 store 中的 JSON 文件。每筆記憶都按照自訂命名空間（如資料夾）與獨特鍵（如檔名）組織。這種階層式結構便於整理與擷取資訊。下列程式碼示範如何使用 InMemoryStore 放入、讀取與搜尋記憶。

```python
from langgraph.store.memory import InMemoryStore


# A placeholder for a real embedding function
def embed(texts: list[str]) -> list[list[float]]:
    # In a real application, use a proper embedding model
    return [[1.0, 2.0] for _ in texts]


# Initialize an in-memory store. For production, use a database-backed store.
store = InMemoryStore(index={"embed": embed, "dims": 2})

# Define a namespace for a specific user and application context
user_id = "my-user"
application_context = "chitchat"
namespace = (user_id, application_context)

# 1. Put a memory into the store
store.put(
    namespace,
    "a-memory",  # The key for this memory
    {
        "rules": [
            "User likes short, direct language",
            "User only speaks English & python",
        ],
        "my-key": "my-value",
    },
)

# 2. Get the memory by its namespace and key
item = store.get(namespace, "a-memory")
print("Retrieved Item:", item)

# 3. Search for memories within the namespace, filtering by content
# and sorting by vector similarity to the query.
items = store.search(
    namespace,
    filter={"my-key": "my-value"},
    query="language preferences",
)
print("Search Results:", items)
```

## Vertex 記憶庫（Vertex Memory Bank）

Memory Bank 是 Vertex AI Agent Engine 中的受管服務，為代理提供持久的長期記憶。該服務使用 Gemini 模型非同步分析對話歷史，以萃取關鍵事實與使用者偏好。

這些資訊會永久儲存，依預先定義的範圍（例如使用者 ID）進行組織，並智慧地更新以整合新資料與解決矛盾。開啟新 Session 時，代理會透過完整資料召回或使用嵌入向量的相似度搜尋來擷取相關記憶。此流程讓代理得以在多個 Session 中維持連續性，並根據回想出的資訊提供個人化回應。

代理的執行器（Runner）會與 VertexAiMemoryBankService 互動，須先完成初始化。該服務負責自動儲存代理在對話期間產生的記憶。每筆記憶都標記唯一的 `USER_ID` 與 `APP_NAME`，以確保日後能正確擷取。

```python
from google.adk.memory import VertexAiMemoryBankService


agent_engine_id = agent_engine.api_resource.name.split("/")[-1]

memory_service = VertexAiMemoryBankService(
    project="PROJECT_ID",
    location="LOCATION",
    agent_engine_id=agent_engine_id,
)

session = await session_service.get_session(
    app_name=app_name,
    user_id="USER_ID",
    session_id=session.id,
)

await memory_service.add_session_to_memory(session)
```

Memory Bank 與 Google ADK 無縫整合，提供開箱即用的體驗。對於其他代理框架使用者，例如 LangGraph 與 CrewAI，Memory Bank 也透過直接 API 呼叫提供支援。網路上有許多展示這些整合的程式碼範例，供有興趣的讀者參考。

## 快速瀏覽（At a Glance）

**內容（What）**：代理系統需要記住過往互動的資訊，才能執行複雜任務並提供一致體驗。若缺乏記憶機制，代理會變得無狀態，無法維持對話脈絡、從經驗中學習或為使用者客製化回應。這根本上限制代理只能處理簡單的一次性互動，無法應對多步驟流程或不斷演變的使用者需求。核心問題在於如何有效管理單次對話的即時暫存資訊，以及隨時間累積的大量持久知識。

**原因（Why）**：標準化的解決方案是實作區分短期與長期儲存的雙元記憶系統。短期語境記憶保留 LLM 語境視窗內的近期互動資料，以維持對話流暢。需要持久保存的資訊則透過外部資料庫（通常是向量儲存）以高效率、語意化方式檢索。像 Google ADK 這類代理框架提供具體元件來管理這些需求，例如 Session 負責對話線索，State 負責暫存資料。專用的記憶服務（MemoryService）則介接長期知識庫，讓代理能擷取並整合過往相關資訊到當前語境中。

**使用準則（Rule of thumb）**：當代理需要做的不只是回答單一問題時，就應使用此模式。它對必須在對話期間維持語境、追蹤多步驟任務進度或透過回想使用者偏好與歷史進行個人化互動的代理而言不可或缺。每當期望代理能根據過往成功、失敗或新獲資訊來學習或調整時，都應實施記憶體管理。

**視覺摘要（Visual summary）**：

![Memory Management Design Pattern](../assets/Memory_Management_Design_Pattern.png)

圖 1：記憶體管理設計模式（Memory management design pattern）

## 重要重點（Key Takeaways）

快速複習記憶體管理的重點：

* 記憶（memory）對代理追蹤事項、學習與個人化互動極為重要。
* 會話式人工智慧（conversational AI）同時仰賴短期記憶取得單一聊天的即時語境，以及長期記憶在多個 Session 間維持持久知識。
* 短期記憶（short-term memory，立即資訊）是暫時的，常受限於 LLM 的語境視窗或框架傳遞語境的方式。
* 長期記憶（long-term memory，可持續資訊）會將資料儲存在外部儲存體，例如向量資料庫，並透過搜尋存取。
* ADK 等框架提供特定元件，例如 Session（聊天線索）、State（暫存聊天資料）與 記憶服務（MemoryService，可搜尋的長期知識），用來管理記憶。
* ADK 的 Session 服務（SessionService）負責整個聊天工作階段生命週期，包括歷史（events）與暫存資料（state）。
* ADK 的 `session.state` 是儲存暫存聊天資料的字典。前綴（`user:`、`app:`、`temp:`）說明資料歸屬與是否持久。
* 在 ADK 中，應透過 `EventActions.state_delta` 或 `output_key` 在新增事件時更新 state，而非直接修改 state 字典。
* ADK 的記憶服務（MemoryService）用於把資訊放進長期儲存，並讓代理搜尋它，通常透過工具完成。
* LangChain 提供實用工具，例如 ConversationBufferMemory，自動把單一對話的歷史注入提示，讓代理能回想即時語境。
* LangGraph 透過儲存機制，讓代理在不同使用者 Session 間保存並擷取語意事實、事件記憶甚至可更新的程序規則，實現進階長期記憶。
* Memory Bank 是受管服務，可自動擷取、儲存並召回使用者專屬資訊，讓代理在 Google ADK、LangGraph 與 CrewAI 等框架中進行個人化且持續的對話。

## 結論（Conclusion）

本章深入探討代理系統中記憶體管理的關鍵任務，說明短期語境與長期知識之間的差異。我們討論了這些記憶類型的架構與應用情境，展展示如何在建構更聰明、能記住資訊的代理時加以運用。我們詳細檢視了 Google ADK 如何提供 Session、State 與 記憶服務（MemoryService）等特定元件來處理這些需求。既然已掌握代理如何記住資訊（無論短期或長期），下一章「學習與適應（Learning and Adaptation）」將探討代理如何根據新經驗或資料改變思考、行動或知識。

## References

1. ADK Memory, [https://google.github.io/adk-docs/sessions/memory/](https://google.github.io/adk-docs/sessions/memory/)
2. LangGraph Memory, [https://langchain-ai.github.io/langgraph/concepts/memory/](https://langchain-ai.github.io/langgraph/concepts/memory/)
3. Vertex AI Agent Engine Memory Bank, [https://cloud.google.com/blog/products/ai-machine-learning/vertex-ai-memory-bank-in-public-preview](https://cloud.google.com/blog/products/ai-machine-learning/vertex-ai-memory-bank-in-public-preview)
