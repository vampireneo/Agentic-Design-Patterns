# 第12章：例外處理與復原（Exception Handling and Recovery）

為了讓人工智能代理（AI agents）在多變的真實世界環境中可靠運作，它們必須能夠處理突發狀況、錯誤與故障。正如人類會調整以應對意料之外的障礙，智慧代理需要強韌的系統來偵測問題、啟動復原程序，或至少確保在失敗時仍能受控。這項關鍵需求構成了例外處理與復原模式（Exception Handling and Recovery pattern）的基礎。

這個模式專注於打造高度耐用且有韌性的代理，使其在面對各式困難與異常時仍能維持不中斷的功能與營運完整性。它強調主動準備（proactive preparation）與被動應對（reactive strategies）的重要性，即使遭遇挑戰亦能確保持續運作。這種適應力對代理在複雜且難以預測的情境中成功運作至關重要，最終提升整體效能與可信度。

處理突發事件的能力確保這些人工智能系統不僅具備智慧，還同時穩定可靠，從而增強部署與表現上的信心。整合全面的監控與診斷工具（monitoring and diagnostic tools）更進一步強化代理快速識別與處理問題的能力，避免潛在中斷，並在持續變化的條件下維持更順暢的運作。這些進階系統對維護人工智能運作的完整性與效率至關重要，鞏固其管理複雜性與不可預測性的能力。

此模式有時會與反思（reflection）一起使用。例如，若初次嘗試失敗並觸發例外，反思程序可以分析失敗原因，透過改良的方法（例如更佳的提示）再次嘗試以解決錯誤。

## 例外處理與復原模式概覽（Exception Handling and Recovery Pattern Overview）

例外處理與復原模式（Exception Handling and Recovery pattern）旨在滿足人工智能代理管理營運失敗的需求。該模式涵蓋預先預測潛在問題，例如工具錯誤或服務無法使用，並制定緩解策略。這些策略可能包括錯誤記錄（error logging）、重試（retries）、備援方案（fallbacks）、優雅降級（graceful degradation）與通知（notifications）。此外，該模式強調復原機制，例如狀態回滾（state rollback）、診斷（diagnosis）、自我修正（self-correction）與升級處理（escalation），以恢復代理到穩定運作。實作這個模式能提升人工智能代理的可靠度與韌性，使其能在不可預測的環境中運作。實際應用範例包括聊天機器人處理資料庫錯誤、交易機器人處理金融錯誤，以及智慧家居代理處理裝置故障。此模式確保代理在面對複雜性與失敗時仍能有效運作。

![Key Components of Exception Handling and Recovery for AI agents](../assets/Key_Components_of_Exception_Handling_and_Recovery_for_AI_agents.png)

圖1：人工智能代理例外處理與復原的關鍵元件（Key components of exception handling and recovery for AI agents）

**錯誤偵測（Error Detection）：** 這涉及在問題發生時精確識別營運問題。可能表現為無效或格式錯誤的工具輸出、特定的 API 錯誤（例如 404 Not Found 或 500 Internal Server Error）、服務或 API 反應時間異常偏長，或偏離預期格式的不連貫與無意義回應。此外，也可以由其他代理或專門的監控系統進行監控，以更主動地偵測異常，使系統在問題惡化前捕捉潛在風險。

**錯誤處理（Error Handling）：** 一旦偵測到錯誤，就需要周詳的應對計畫。這包括在記錄檔中精確紀錄錯誤細節以利後續除錯與分析（logging）。對於暫時性錯誤，帶著些許調整參數重試操作或請求可能是一個可行策略（retries）。採用替代策略或方法（fallbacks）可確保某些功能得以維持。若無法立即完全復原，代理可維持部分功能以提供一定價值（graceful degradation）。最後，對需要人為介入或協作的情況，提醒人類操作員或其他代理至關重要（notification）。

**復原（Recovery）：** 此階段關乎在錯誤發生後將代理或系統恢復至穩定且可運作的狀態。可能需要回復近期的變更或交易以消除錯誤影響（state rollback）。為避免再次發生，深入調查錯誤原因至關重要。透過自我修正機制或重新規畫流程，調整代理的計畫、邏輯或參數，以免未來再犯同樣錯誤。在複雜或嚴重的情況下，將問題交由人類操作員或更高層系統處理（escalation）可能是最佳方案。

實作這套穩健的例外處理與復原模式（exception handling and recovery pattern）能將人工智能代理從脆弱不可靠的系統，轉變為能在充滿挑戰且高度不可預測環境中有效且具韌性的可靠組件。如此確保代理維持功能、減少停機時間，並在面對突發問題時仍提供順暢且可信賴的體驗。

## 實際應用與使用案例（Practical Applications & Use Cases）

在無法保證完美條件的真實世界場景中部署代理時，例外處理與復原（Exception Handling and Recovery）尤為關鍵。

* **客戶服務聊天機器人（Customer Service Chatbots）：** 如果聊天機器人嘗試存取客戶資料庫而資料庫暫時停機，它不應當當機。相反地，它應偵測 API 錯誤，通知使用者暫時性問題，或許建議稍後再試，或將查詢升級給真人代理。
* **自動化金融交易（Automated Financial Trading）：** 交易機器人在執行交易時可能遇到「資金不足」錯誤或「市場關閉」錯誤。它需要透過記錄錯誤、避免重複嘗試同樣的無效交易，並視情況通知使用者或調整策略來處理這些例外。
* **智慧家居自動化（Smart Home Automation）：** 控制智慧照明的代理可能因網絡問題或裝置故障而無法開燈。它應偵測失敗、嘗試重試，若仍未成功，就通知使用者無法開燈並建議手動介入。
* **資料處理代理（Data Processing Agents）：** 負責處理一批文件的代理可能遇到損毀檔案。它應略過損毀檔案、記錄錯誤、繼續處理其他檔案，並在結束時報告被略過的檔案，而非中斷整個流程。
* **網頁爬蟲代理（Web Scraping Agents）：** 當網頁爬蟲遇到 CAPTCHA、網站結構變更或伺服器錯誤（例如 404 Not Found、503 Service Unavailable）時，必須妥善處理。可能的作法包括暫停、使用代理伺服器，或回報失敗的特定 URL。
* **機器人與製造（Robotics and Manufacturing）：** 執行組裝任務的機械臂可能因對位不準而無法抓取元件。它需要透過感測器回饋偵測失敗、嘗試重新調整並再次抓取，若問題持續，就提醒人類操作員或改換其他元件。

總之，此模式是構建在真實世界複雜情境中仍能保持智慧、可靠、具韌性且友善使用者的代理之根本。

## 實作範例（Hands-On Code Example, ADK）

例外處理與復原（exception handling and recovery）對系統的堅固性與可靠性至關重要。以代理對失敗工具呼叫的回應為例。此類失敗可能源於錯誤的工具輸入或工具所依賴的外部服務問題。

```python
from google.adk.agents import Agent, SequentialAgent


# Agent 1: Tries the primary tool. Its focus is narrow and clear.
primary_handler = Agent(
    name="primary_handler",
    model="gemini-2.0-flash-exp",
    instruction="""
    Your job is to get precise location information. Use the get_precise_location_info
    tool with the user's provided address.
    """,
    tools=[get_precise_location_info],
)

# Agent 2: Acts as the fallback handler, checking state to decide its action.
fallback_handler = Agent(
    name="fallback_handler",
    model="gemini-2.0-flash-exp",
    instruction="""
    Check if the primary location lookup failed by looking at state["primary_location_failed"].
    - If it is True, extract the city from the user's original query and use the get_general_area_info tool.
    - If it is False, do nothing.
    """,
    tools=[get_general_area_info],
)

# Agent 3: Presents the final result from the state.
response_agent = Agent(
    name="response_agent",
    model="gemini-2.0-flash-exp",
    instruction="""
    Review the location information stored in state["location_result"]. Present this information
    clearly and concisely to the user. If state["location_result"] does not exist or is empty,
    apologize that you could not retrieve the location.
    """,
    tools=[],  # This agent only reasons over the final state.
)

# The SequentialAgent ensures the handlers run in a guaranteed order.
robust_location_agent = SequentialAgent(
    name="robust_location_agent",
    sub_agents=[primary_handler, fallback_handler, response_agent],
)
```

這段程式碼使用 ADK 的 SequentialAgent 組合三個子代理，定義了一個強健的地點搜尋系統。`primary_handler` 是第一個代理，嘗試使用 `get_precise_location_info` 工具取得精確地理資訊。`fallback_handler` 作為備援，透過檢查狀態變數確認主要查詢是否失敗。若主要查詢失敗，備援代理會從使用者查詢中擷取城市並使用 `get_general_area_info` 工具。`response_agent` 是序列中的最後一個代理，負責檢視狀態中儲存的地點資訊。此代理旨在向使用者呈現最終結果，若找不到任何地點資訊，就會致歉。SequentialAgent 確保三個代理按預定順序執行，提供層疊式的地點資訊擷取流程。

## 重點一覽(At a Glance)

**內容（What）：** 在真實世界環境運作的人工智能代理不可避免會遭遇突發狀況、錯誤與系統故障。這些干擾可能涵蓋工具失敗、網絡問題或無效資料，威脅代理完成任務的能力。若缺乏有結構的問題處理方式，代理可能變得脆弱、不可靠，並在遭遇意外阻礙時完全失敗，難以部署於要求穩定表現的重要或複雜應用。

**原因（Why）：** 例外處理與復原模式（Exception Handling and Recovery pattern）提供標準化方案，以打造堅固且具韌性的人工智能代理。它賦予代理預測、管理並從營運失敗中復原的能力。該模式包含主動的錯誤偵測，如監控工具輸出與 API 回應，以及被動的應對策略，如記錄以利診斷、重試暫時性失敗或使用備援機制。對於更嚴重的問題，模式定義復原流程，包括回復至穩定狀態、透過調整計畫進行自我修正，或將問題升級交由人類操作員處理。這種系統化方法確保代理能維持營運完整性、從失敗中學習，並在不可預測的情境中可靠運作。

**經驗法則（Rule of Thumb）：** 只要人工智能代理部署於動態真實環境，可能面臨系統故障、工具錯誤、網絡問題或不可預測輸入，且營運可靠性是關鍵需求，就應採用此模式。

**視覺摘要（Visual Summary）：**

![Exception Handling Pattern](../assets/Exception_Handling_Pattern.png)

圖2：例外處理模式（Exception handling pattern）

## 核心重點（Key Takeaways）

必須記住的重點：

* 例外處理與復原（Exception Handling and Recovery）是打造堅固可靠代理的必要條件。
* 此模式涉及偵測錯誤、妥善處理並實施復原策略。
* 錯誤偵測可包含驗證工具輸出、檢查 API 錯誤碼與使用逾時設定。
* 應對策略包括記錄（logging）、重試（retries）、備援方案（fallbacks）、優雅降級（graceful degradation）與通知（notifications）。
* 復原專注於透過診斷（diagnosis）、自我修正（self-correction）或升級處理（escalation）恢復穩定運作。
* 此模式確保代理即使在不可預測的真實世界環境中也能有效運作。

## 結論（Conclusion）

本章探討例外處理與復原模式（Exception Handling and Recovery pattern），該模式對開發堅固可靠的人工智能代理至關重要。它說明人工智能代理如何識別並管理突發問題、實施適當的應對措施，並恢復至穩定營運狀態。本章討論此模式的各種面向，包括透過記錄、重試與備援等機制偵測與處理錯誤，以及協助代理或系統回復正常運作的策略。章節亦透過多個領域的實際應用示例，展示此模式在處理真實世界複雜性與潛在失敗方面的相關性，凸顯為人工智能代理配備例外處理能力如何提升其在動態環境中的可靠性與適應力。

## References

1. McConnell, S. (2004). *Code Complete (2nd ed.)*. Microsoft Press.
2. Shi, Y., Pei, H., Feng, L., Zhang, Y., & Yao, D. (2024). *Towards Fault Tolerance in Multi-Agent Reinforcement Learning*. arXiv preprint arXiv:2412.00534.
3. O'Neill, V. (2022). *Improving Fault Tolerance and Reliability of Heterogeneous Multi-Agent IoT Systems Using Intelligence Transfer*. Electronics, 11(17), 2724.
