# 附錄E - 命令列上的人工智能代理（AI Agents on the CLI）

## 引言

開發者的命令列（command line）一直是精準、指令式指令的堡壘，如今正經歷深刻轉變。它正由簡單的殼層（shell）演化成由全新工具類別驅動的智能協作工作空間：人工智能代理命令列介面（AI Agent Command-Line Interfaces，CLI）。這些代理（agents）不僅僅執行指令；它們能理解自然語言，維持對整個程式碼庫的脈絡，並能執行自動化複雜且多步驟的任務，大幅簡化開發生命週期中的關鍵工序。

本指南深入剖析這個新興領域中的四個領先者，探討其獨特優勢、理想使用情境與截然不同的理念，協助你判斷哪一款工具最適合你的工作流程。值得注意的是，針對某個工具所提供的許多範例，其實其他代理也多能完成。這些工具之間的關鍵差異，往往在於它們在特定任務上所能達到的成果品質、效率與細緻度。評估這些能力的專門基準（benchmarks）亦已存在，並會在後續章節中討論。

## Claude CLI（Claude Code）

Anthropic 的 Claude 命令列介面（Claude CLI）被設計成具備高層次理解力的編碼代理，能以全面視角洞悉專案架構。它的核心優勢在於其「代理式」（agentic）特質，讓它能為複雜、多步驟的任務建立程式庫的心智模型。互動方式高度對話化，宛如結伴編程（pair programming）期間會在執行前解說計畫，因而特別適合從事大型專案、需進行重大重構或實作具廣泛架構影響功能的專業開發者。

**範例使用情境：**

1. **大規模重構：** 你可以指示：「我們目前的使用者驗證依賴工作階段（session）cookie。請重構整個程式碼庫以改用無狀態 JSON 網頁權杖（JWT），更新登入／登出端點、中介軟件（middleware）與前端權杖處理。」Claude 會閱讀所有相關檔案並執行協調後的修改。
2. **應用程式介面（API）整合：** 在提供新天氣服務的 OpenAPI 規格後，你可以說：「整合這個新的天氣 API。建立負責 API 呼叫的服務模組，加入一個顯示天氣的新元件，並更新主控制台（dashboard）以納入它。」
3. **文件產生：** 指向一個程式結構複雜但缺乏文件的模組，你可以請求：「分析 `./src/utils/data_processing.js` 檔案，為每個函式產生完整的 TypeScript 文件註解（TSDoc），說明其用途、參數與回傳值。」

Claude CLI 作為專門化的編碼助手，內建核心開發任務所需的工具，包括檔案擷取、程式碼結構分析與編輯生成。它與 Git 深度整合，能直接管理分支與提交（commit）。代理的可擴充性由多工具控制協定（Multi-tool Control Protocol，MCP）所驅動，使用者得以定義並整合自訂工具，從而與私有 API 互動、查詢資料庫或執行專案專屬指令。這種架構讓開發者成為代理功能範圍的裁決者，有效地將 Claude 定位成由使用者定義工具增強的推理引擎。

## Gemini CLI

Google 的 Gemini 命令列介面（Gemini CLI）是一款兼具強大功能與易用性的多才多藝開源人工智能代理。它以先進的 Gemini 2.5 Pro 模型、龐大脈絡視窗與多模態（multimodal）處理能力（可同時處理影像與文字）脫穎而出。開源屬性、寬鬆的免費方案，以及「推理與行動」（Reason and Act）循環，讓它對從興趣開發者到企業開發者的廣大受眾都透明、可控且全面，尤其是身處 Google 雲端（Google Cloud）生態系的使用者。

**範例使用情境：**

1. **多模態開發：** 你提供設計檔中的網頁元件截圖（gemini describe component.png），並指示：「撰寫 HTML 與 CSS 程式碼，以建構一個外觀完全一致的 React 元件，確保它具有回應式設計（responsive）。」
2. **雲端資源管理：** 利用內建的 Google 雲端整合，你可以下達指令：「尋找生產專案中所有版本低於 1.28 的 Google Kubernetes Engine（GKE）叢集，並產生逐一升級的 gcloud 指令。」
3. **企業工具整合（透過 MCP）：** 有位開發者為 Gemini 提供一個名為 get-employee-details 的自訂工具，連接公司內部的人力資源應用程式介面（HR API）。提示為：「撰寫我們新入職同事的歡迎文件。首先使用 get-employee-details --id=E90210 工具取得姓名與團隊，然後將資料填入 welcome_template.md。」
4. **大規模重構：** 某位開發者需要重構大型 Java 程式碼庫，以將淘汰的記錄（logging）函式庫替換成全新結構化記錄框架。他們可以使用 Gemini 提示：「閱讀 'src/main/java' 目錄下所有 *.java 檔案。對每個檔案，將 'org.apache.log4j' 的匯入與其 'Logger' 類別替換為 'org.slf4j.Logger' 與 'LoggerFactory'。重寫記錄初始化與所有 .info()、.debug() 和 .error() 呼叫，使其採用具鍵值配對的全新結構化格式。」

Gemini CLI 配備一系列內建工具，讓它得以與環境互動。其中包括檔案系統操作工具（例如讀取與寫入）、用於執行指令的殼層工具，以及透過網頁擷取與搜尋訪問互聯網的工具。為了獲得更廣的脈絡，它會使用專門工具一次讀取多個檔案，並透過記憶工具保存資訊以供日後工作階段使用。這些功能建構於安全基礎之上：沙盒化（sandboxing）將模型行動隔離以降低風險，而 MCP 伺服器則充當橋樑，讓 Gemini 得以安全地連接你的本地環境或其他應用程式介面（API）。

## Aider

Aider 是一款開源人工智能編碼助手，透過直接操作檔案並提交 Git 修改，成為真正的結伴編程夥伴。它的標誌性特色是直接性：應用修改、執行測試以驗證結果，並自動提交每項成功變更。由於不受模型限制（model-agnostic），使用者能完全掌控成本與能力。其以 Git 為核心的工作流程，特別適合重視效率、掌控感與透明可稽核程式碼修改紀錄的開發者。

**範例使用情境：**

1. **測試驅動開發（Test-Driven Development，TDD）：** 開發者可以說：「為計算數字階乘的函式建立一個會失敗的測試。」Aider 會撰寫測試並看到失敗後，再接續提示：「現在撰寫程式碼讓測試通過。」Aider 實作函式並再次執行測試以確認。
2. **精準除錯：** 在收到錯誤報告後，你可以指示 Aider：「billing.py 中的 `calculate_total` 函式在閏年會失敗。請將檔案加入脈絡、修正錯誤，並針對現有測試套件驗證修復。」
3. **相依套件更新：** 你可以下達指示：「我們的專案使用過時版本的 'requests' 函式庫。請檢視所有 Python 檔案，更新匯入語句與任何已淘汰的函式呼叫以相容最新版本，並更新 requirements.txt。」

## GitHub Copilot CLI

GitHub Copilot 命令列介面（GitHub Copilot CLI）將受歡迎的人工智能結伴編程工具延伸至終端機，其主要優勢在於與 GitHub 生態的原生深度整合。它能理解 *GitHub* 專案脈絡。其代理能力允許被指派 GitHub 問題單（issue），著手修復並提交交由人工審查的拉取請求（pull request）。

**範例使用情境：**

1. **自動化問題修復：** 經理將一張錯誤票（例如：「Issue #123: Fix off-by-one error in pagination」）指派給 Copilot 代理。代理會檢出新分支、撰寫程式碼，並提交引用該問題的拉取請求，全程無需開發者手動介入。
2. **具程式庫脈絡的問與答：** 新加入團隊的開發者可詢問：「在這個程式庫（repository）中，資料庫連線邏輯定義在哪裡？需要哪些環境變數？」Copilot CLI 利用對整個程式庫的了解，提供附帶檔案路徑的精準答案。
3. **殼層指令助手：** 當使用者不確定複雜的殼層指令時，可詢問：gh? 找出所有大於 50MB 的檔案，壓縮它們並放到 archive 資料夾。Copilot 會產生完成任務所需的精確殼層指令。

## Terminal-Bench：命令列人工智能代理的基準

Terminal-Bench 是專為評估人工智能代理在命令列介面中執行複雜任務能力而設計的創新評測框架。終端機被視為人工智能代理運作的最佳環境，因其文字為主且具沙盒化特性。首個版本 Terminal-Bench-Core-v0 收錄 80 個手工策劃的任務，涵蓋科學工作流程與資料分析等領域。為確保公平比較，團隊開發了 Terminus，一個極簡代理，作為各語言模型的標準化測試平台。該框架以擴充性為設計核心，允許透過容器化或直接連接整合多種代理。未來發展包括實現大量平行評測與納入既有基準。專案亦鼓勵開源貢獻，擴充任務並共同提升框架。

## 結論

這些強大人工智能命令列代理的出現，標誌軟件開發的根本轉變，將終端機轉化為動態且具協作性的環境。如我們所見，並無單一「最佳」工具；取而代之的是多樣化的生態系，每個代理都帶來專門強項。理想選擇完全取決於開發者需求：Claude 適合處理複雜架構任務，Gemini 擅長多元且多模態問題解決，Aider 適合以 Git 為核心並直接編輯程式碼的工作流程，而 GitHub Copilot 則在 GitHub 工作流程中實現無縫整合。隨著這些工具持續演進，善用它們的能力將成為關鍵技能，從根本上改變開發者建構、除錯與管理軟件的方式。

## References

1. Anthropic. *Claude*. [https://docs.anthropic.com/en/docs/claude-code/cli-reference](https://docs.anthropic.com/en/docs/claude-code/cli-reference)
2. Google Gemini Cli [https://github.com/google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli)
3. Aider. [https://aider.chat/](https://aider.chat/)
4. GitHub *Copilot CLI* [https://docs.github.com/en/copilot/github-copilot-enterprise/copilot-cli](https://docs.github.com/en/copilot/github-copilot-enterprise/copilot-cli)
5. Terminal Bench: [https://www.tbench.ai/](https://www.tbench.ai/)
