# 代理型設計模式（Agentic Design Patterns）

本版本庫收錄 Antonio Gulli 與 Mauro Sauco 合著的《Agentic Design Patterns》全文，由 Tom Mathews 彙編整理，方便社群查閱與參考。

![Agentic Design Patterns - Book Cover](assets/Agentic_Design_Patterns_Book_Cover.png)

## 作者與鳴謝

- **作者：** [Antonio Gulli](https://www.linkedin.com/in/searchguy/) 與 [Mauro Sauco](https://www.linkedin.com/in/maurosauco/)
- **彙編：** [Tom Mathews](https://www.linkedin.com/in/mathews-tom/)

### 這本書有甚麼特別之處？

這本 424 頁的指南，正面回應我們在打造智能、自主人工智能（AI）系統時遇到的真實挑戰，在理論與實作之間架起橋樑——正是當前整個領域最需要的。
無論你是工程師、研究員還是產品經理，只要希望從基礎大型語言模型（Large Language Model，簡稱 LLM）應用邁向真正可靠的 AI 代理（Agent）系統，這本書就是最佳資源。

內容涵蓋關鍵的代理型模式，例如提示鏈接（Prompt Chaining）、路由（Routing）、規劃（Planning）與多代理系統（Multi-Agent Systems），並配合可運行的程式碼示例。
你還會找到關於工具使用（Tool Use）、記憶體管理（Memory Management）、檢索增強生成（RAG）實作的完整指南，以及推理技術（Reasoning Techniques）、代理間通訊（Inter-Agent Communication）等進階主題。

書中你將看到：

- **真實程式碼示例：** 不僅闡述理論，亦提供可直接運行的實作。
- **經得起考驗的模式：** 記憶體處理、異常邏輯、資源控制與安全護欄（Guardrails）。
- **進階技術：** 多代理（Multi-Agent）協作、代理間訊息傳遞、人類在迴圈中（Human in the Loop）。
- **完整介紹模型上下文協議（Model Context Protocol，MCP）：** 整合工具與代理的關鍵框架。

全書分為四個部分，共 21 個核心模式：

1. 基礎模式（提示鏈接、路由、工具使用）
2. 進階系統（記憶體、學習、監控）
3. 上線考量（錯誤處理、安全、評估）
4. 多代理架構

大部分 AI 內容仍停留在「如何調用 API」這一步。然而在真實系統，你需要思考：

- 如果代理在任務途中停滯，應該如何處理？
- 如何在長時間對話中保留關鍵記憶？
- 當同時運行 10 個以上代理時，如何避免失控？

這本書以可落實的模式，全面回答上述問題。僅附錄部分就超過 70 頁，內容涵蓋進階提示技巧與代理型框架概覽，極具參考價值。

## 目錄

### 引言

- [獻詞](00-Introduction/01-Dedication-1cQ61mNpiWn6eSORmWjEjF44vN2Lpba8kyKmNwIC60ig.md)
- [鳴謝](00-Introduction/02-Acknowledgment-1u2y6tY48bw8nriDUuwWEf9s8g66vyIqBKSKZDOS-n0s.md)
- [前言](00-Introduction/03-Foreword-18Q9kfZuCTL37ztrSjLxwf8Elr5UfAiAavmnj0IqSpbU.md)
- [思想領袖的視角：權力與責任](00-Introduction/04-A_Thought_Leaders_Perspective_Power_and_Responsibility-1PWhaXD_UNKgJaxYe3JBxRFRt3_B8Wm67CFxtSBQ4LkU.md)
- [導言](00-Introduction/05-Introduction-1K5jwqB6jh20uHL0TTWxqWOxFk-dzFxRvHzrRRV79hrg.md)
- [甚麼令一個 AI 系統成為代理？](00-Introduction/06-What_makes_an_AI_system_an_Agent-1Nw6hRa7ItdLr_Tj5hF2q-OH8B_uPKb--RLn8SXZKA94.md)

### 第一部分：基礎模式

- [第一章：提示鏈結（Prompt Chaining）](01-Part_One/Chapter_1-Prompt_Chaining-1flxKGrbnF2g8yh3F-oVD5Xx7ZumId56HbFpIiPdkqLI.md)
- [第二章：路由（Routing）](01-Part_One/Chapter_2-Routing-1ux_n8n3T4bYndOjs1DKW5ccpC802KISdy2IWnlvYbas.md)
- [第三章：並行化](01-Part_One/Chapter_3-Parallelization-1XVMp4RcRkoUJTVbrP2foWZX703CUJpWkrhyFU2cfUOA.md)
- [第四章：反思](01-Part_One/Chapter_4-Reflection-1HXXJOQIMWowtLw4WMiSR360caDAlZPtl5dPPgvq9IT4.md)
- [第五章：工具使用（Tool Use，函數呼叫）](01-Part_One/Chapter_5-Tool_Use_(Function_Calling)-1bE4iMljhppqGY1p48gQWtZvk6MfRuJRCiba1yRykGNE.md)
- [第六章：規劃（Planning）](01-Part_One/Chapter_6-Planning-18vvNESEwHnVUREzIipuaDNCnNAREGqEfy9MQYC9wb4o.md)
- [第七章：多代理協作（Multi-Agent Collaboration）](01-Part_One/Chapter_7-Multi-Agent_Collaboration-1RZ5-2fykDQKOBx01pwfKkDe0GCs5ydca7xW9Q4wqS_M.md)

### 第二部分：進階系統

- [第八章：記憶體管理（Memory Management）](02-Part_Two/Chapter_8-Memory_Management-1asVTObtzIye0I9ypAztaeeI_sr_Hx2TORE02uUuqH_c.md)
- [第九章：學習與適應](02-Part_Two/Chapter_9-Learning_and_Adaptation-1UHTEDCmSM1nwB-iyMoHuYzVcu_B_4KkJ2ITGGUKqo8s.md)
- [第十章：模型上下文協議（Model Context Protocol，MCP）](02-Part_Two/Chapter_10-Model_Context_Protocol_(MCP)-1e6XimYczKmhX9zpqEyxLFWPQgGuG0brp7Hic2sFl_qw.md)
- [第十一章：目標設定與監控](02-Part_Two/Chapter_11-Goal_Setting_and_Monitoring-10ndlCB39BWjyFRWKpcoKib4vuPD1ojD-x0-ynMaf5uw.md)

### 第三部分：部署考量

- [第十二章：例外處理與復原](03-Part_Three/Chapter_12-Exception_Handling_and_Recovery-1C07AuMur6-infwE0viCp4QtAy_wWI-uceFm6MaYHQGk.md)
- [第十三章：人類在迴圈中（Human in the Loop）](03-Part_Three/Chapter_13-Human_in_the_Loop-1ImOZcw6yeb7a-uRBMNP1VdovYfyip4IdsAcLu9yue-0.md)
- [第十四章：知識檢索（檢索增強生成 RAG）](03-Part_Three/Chapter_14-Knowledge_Retrieval_(RAG)-1v96Oobio6xDOqbK8ejsXjmOc4Dp2uoLMo5_gfJgi-NE.md)

### 第四部分：多代理架構

- [第十五章：代理間通訊（Inter-Agent Communication，A2A）](04-Part_Four/Chapter_15-Inter_Agent_Communication_(A2A)-1H6HmUYcy5kugt5gt7Kh2Zzb8C62d5pu36RsgMNDCX24.md)
- [第十六章：資源感知優化](04-Part_Four/Chapter_16-Resource_Aware_Optimization-1nAN58l6JjqEJHk43126uh7xgdEblCpcbsNUHXgtBmJQ.md)
- [第十七章：推理技術（Reasoning Techniques）](04-Part_Four/Chapter_17-Reasoning_Techniques-1Yt1W_hLaC6ZNgJXfT4W6NrCL4TzNVdKOX50kgpHiIq4.md)
- [第十八章：護欄與安全模式（Guardrails and Safety Patterns）](04-Part_Four/Chapter_18-Guardrails_Safety_Patterns-1Gpc5af_okze1kprRLohP6-81e1KwL6HggjeLvxQyIuk.md)
- [第十九章：評估與監控](04-Part_Four/Chapter_19-Evaluation_and_Monitoring-1G3zOZM2ZOd0gUp5dy66FUjKMOcALh9l-JpvPxgGMm8w.md)
- [第二十章：優先次序（Prioritization）](04-Part_Four/Chapter_20-Prioritization-1qyXxGM2hNqW_qjXuBFxrEUeoYVO79BoW1ogKu1bfdCY.md)
- [第二十一章：探索與發現（Exploration and Discovery）](04-Part_Four/Chapter_21-Exploration_and_Discovery-1zeeMVTqjqRIli6G9MMWThhoQhvKqLOjJF2EHHUXLhdk.md)

### 附錄

- [附錄 A：進階提示技巧（Advanced Prompting Techniques）](05-Appendix/Appendix_A-Advanced_Prompting_Techniques-1V7EKEWibOH6IhHD_PtbFZiml492-2191jDQCcTkhtTI.md)
- [附錄 B：AI 代理互動——從圖形使用者介面延伸至真實世界環境（AI Agentic Interactions from GUI to Real World Environment）](05-Appendix/Appendix_B-AI_Agentic_Interactions_From_GUI_to_Real_World_Environment-11pma_tCoC7uZ2SFKjcR5KyIq0_ooMGSoadI6f9mxG2I.md)
- [附錄 C：代理式框架快速概覽（Quick Overview of Agentic Frameworks）](05-Appendix/Appendix_C-Quick_Overview_of_Agentic_Frameworks-151rGsiEYOkXUcNDRus_N8TxxuvjoyTDViBhzt9z0Mfw.md)
- [附錄 D：使用 AgentSpace 建立代理（Building an Agent with AgentSpace，僅線上提供）](05-Appendix/Appendix_D-Building_an_Agent_with_AgentSpace_(on_line_only)-1bDRJ8mKtLTeWNC-cGD0Cr8pEJQgJHNcjqz5ekloAjaE.md)
- [附錄 E：命令列上的人工智能代理（AI Agents on the CLI）](05-Appendix/Appendix_E-AI_Agents_on_the_CLI-1W4znto0a8Ikajw5a4tEyRAaB2nJPJw_iFc4w4qNnjho.md)
- [附錄 F：幕後揭秘——深入了解代理推理引擎（Under the Hood: An Inside Look at the Agents' Reasoning Engines）](05-Appendix/Appendix_F-Under_the_Hood_An_Inside_Look_at_the_Agents_Reasoning_Engines-14q3fQ-FZmDgiughno_WLSILMWkURvUgR7mlGiFtvwd4.md)
- [附錄 G：編碼代理（Coding Agents）](05-Appendix/Appendix_G-Coding_Agents-1tVyhgwrD4fu_D_pHUrwhNxoguRG3tLc1KObXFxrxE_s.md)

## 授權

本專案採用 MIT 授權條款，詳見 [LICENSE](LICENSE)。
