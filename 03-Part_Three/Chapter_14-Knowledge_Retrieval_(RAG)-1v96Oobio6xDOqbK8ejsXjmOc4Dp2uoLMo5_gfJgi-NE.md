# 第14章：知識檢索（Knowledge Retrieval，RAG）

大型語言模型（Large Language Model，LLM）展現出生成類似人類文本的強大能力。然而，它們的知識庫通常侷限於訓練時期的資料，難以掌握即時資訊、企業內部資料或高度專門的細節。知識檢索（Retrieval-Augmented Generation，RAG）正是為了解決這項限制。RAG 讓 LLM 可以存取並整合外部、最新且具情境性的資訊，從而提升輸出的準確性、相關性與事實基礎。

對於人工智慧代理（AI agent）而言，這一能力至關重要，因為它使代理能根據超越靜態訓練資料的即時可驗證資訊來行動與回應。這種能力讓代理可以準確執行複雜任務，例如查閱最新的公司政策以回答特定問題，或在下單前檢視目前庫存。透過整合外部知識，RAG 將代理從單純的對話者，轉變為能夠完成有意義工作的資料導向工具。

## 知識檢索（Knowledge Retrieval，RAG）模式概覽

知識檢索（RAG）模式藉由在生成回應之前賦予 LLM 存取外部知識庫的能力，大幅擴充其實力。RAG 讓 LLM 不再只能倚賴內建的預訓練知識，而能像人類一樣先「查找」資料，例如翻閱書籍或搜尋網路。此流程使 LLM 可以提供更精準、最新且可驗證的答案。

當使用者對採用 RAG 的 AI 系統提出問題或提示時，查詢並不會直接送至 LLM。系統會先探索龐大的外部知識庫——這可能是高度組織化的文件資料庫、資料庫系統或網頁——以尋找相關資訊。這個搜尋並非簡單的關鍵字比對，而是能理解使用者意圖與語意的「語意搜尋（semantic search）」。初步搜尋會擷取最相關的片段或「區塊（chunk）」，再將這些抽取出的內容「增強（augment）」到原始提示中，形成資訊更充分的查詢。最後，這份增強後的提示才送進 LLM，使其在額外脈絡的協助下，生成既流暢自然又以檢索資料為根據的回應。

RAG 框架帶來多項重要好處。它讓 LLM 得以存取最新資訊，克服靜態訓練資料的限制。這種方法也能降低「幻覺（hallucination）」——生成錯誤資訊——的風險，因為回應植基於可驗證的資料。此外，LLM 可以運用企業內部文件或維基中的專業知識。此流程的一大優勢是能提供「引用（citation）」，精準指出資訊來源，進而提升 AI 回應的可信度與可驗證性。

為了全面理解 RAG 的運作，必須先掌握幾個核心概念（見圖 1）：

### 嵌入（Embeddings）

在 LLM 的脈絡下，嵌入是文字（如單字、片語或整篇文件）的數值表示。這些表示以向量（vector）的形式呈現，即一串數字。核心理念在於在數學空間中捕捉語意與不同文字之間的關係。語意相近的文字，其嵌入在向量空間中的距離會比較近。想像一個簡單的二維圖：「cat」可能被表示為座標 (2, 3)，而「kitten」則會接近在 (2.1, 3.1)；相反地，「car」可能位於 (8, 1)，反映其語意差異。實際上，嵌入存在維度遠高於二維的空間，可能有數百甚至數千維，使模型能更細膩地理解語言。

### 文字相似度（Text Similarity）

文字相似度指兩段文字相似程度的量度。這可以是表層層次（檢視字詞重疊，也稱詞彙相似度 lexical similarity），也可以是更深入的語意層面。在 RAG 中，文字相似度對於在知識庫中找到與使用者查詢最相關的資訊至關重要。舉例而言：「What is the capital of France?」與「Which city is the capital of France?」兩句話雖然用詞不同，實際上在詢問同一個問題。一個良好的文字相似度模型會辨識這點，並給予兩句話高分的相似度，即使它們只有少數字詞相同。這類相似度通常透過文字的嵌入來計算。

### 語意相似度與距離（Semantic Similarity and Distance）

語意相似度是更進階的文字相似度，專注於文字的意義與脈絡，而非字面字詞。它旨在判斷兩段文字是否傳達相同概念或想法。語意距離則是反向概念：高語意相似度意味著低語意距離，反之亦然。在 RAG 中，語意搜尋（semantic search）仰賴找到與使用者查詢語意距離最小的文件。例如，「a furry feline companion」與「a domestic cat」除了「a」之外沒有共同字詞，但理解語意相似度的模型會認出它們指的是同一件事，因此視為高度相似。這是因為它們的嵌入在向量空間中十分接近，顯示語意距離很小。正是這種「智慧搜尋」讓 RAG 即使在使用者措辭與知識庫文本不完全一致時，也能找到相關資訊。

![RAG Core Concept: Chunking, Embeddings, and Vector Database](../assets/RAG_Core_Concepts_Chunking_Embeddings_and_Vector_Database.png)

圖 1：RAG 核心概念：文件切塊、嵌入與向量資料庫

### 文件切塊（Chunking of Documents）

切塊是將大型文件分解成較小、較易處理的片段或「區塊」的過程。為了讓 RAG 系統有效運作，它無法將整份大型文件送入 LLM，而是改以這些小區塊處理。文件如何切塊對於維持資訊的脈絡與意義相當重要。例如，與其將 50 頁的使用手冊視為單一區塊，切塊策略會把它拆成章節、段落甚至句子。例如「疑難排解」章節會與「安裝指南」章節分開。當使用者詢問特定問題時，RAG 系統便能擷取最相關的疑難排解區塊，而非整份手冊。這讓檢索流程更快速，提供給 LLM 的資訊也更聚焦於使用者的即時需求。文件完成切塊後，RAG 系統必須運用檢索技術來為查詢找到最相關的內容。主要方法是向量搜尋（vector search），利用嵌入與語意距離尋找在概念上與使用者問題相似的區塊。一種較早但仍有價值的技術是 BM25，這是一種以關鍵字為基礎的演算法，透過詞頻排序區塊而不理解語意。為了兼顧兩者優勢，常會使用混合搜尋（hybrid search），結合 BM25 的關鍵字精準度與語意搜尋的脈絡理解。這種融合能產生更穩健且準確的檢索，同時抓住字面匹配與概念相關性。

### 向量資料庫（Vector Databases）

向量資料庫是專門用來有效儲存與查詢嵌入的資料庫。在文件完成切塊並轉換為嵌入後，這些高維向量會儲存在向量資料庫中。傳統的檢索技術（例如以關鍵字為基礎的搜尋）擅長找到包含查詢字詞的文件，但缺乏深度的語言理解，因此無法辨識「furry feline companion」等同於「cat」。這正是向量資料庫的優勢所在。它們專為語意搜尋打造，把文字以數值向量形式儲存，能根據概念意義而非單純關鍵字重疊來尋找結果。當使用者的查詢同樣轉換成向量後，資料庫會使用高度優化的演算法（例如 HNSW，Hierarchical Navigable Small World）在數百萬個向量中快速搜尋，找出在意義上最近的結果。對於 RAG 而言，這種方法遠勝其他技術，因為即使使用者的措辭與來源文件完全不同，也能找出相關脈絡。從本質上說，其他技術在找「字」，向量資料庫則在找「意義」。此技術有多種實作形式，從托管的資料庫服務（如 Pinecone、Weaviate），到開源方案（如 Chroma DB、Milvus 與 Qdrant）。甚至既有資料庫也能透過擴充功能支援向量搜尋，例如 Redis、Elasticsearch 與 Postgres（搭配 pgvector 擴充套件）。核心的檢索機制通常由 Meta AI 的 FAISS 或 Google Research 的 ScaNN 等函式庫驅動，這些函式庫是系統效率的基石。

### RAG 的挑戰（RAG's Challenges）

儘管 RAG 強大，仍存在不少挑戰。主要問題在於回答查詢所需的資訊可能分散於多個區塊，甚至跨越多份文件。在此情況下，檢索器可能無法收集所有必要的脈絡，導致答案不完整或不精確。系統效能也高度仰賴切塊與檢索流程的品質；若擷取到不相關的區塊，就可能引入雜訊並使 LLM 感到困惑。此外，如何有效整合可能相互矛盾的資訊仍是重大難題。除此之外，RAG 需要事先將整個知識庫預處理並儲存在向量或圖形資料庫等專門系統中，這是一項相當大的工程。因此，這些知識必須定期校準以保持最新，尤其是在面對會不斷更新的來源（如公司維基）時。整個流程還會對效能造成明顯影響，增加延遲、營運成本以及最終提示所需的權杖（token）數量。

總結而言，檢索增強生成（RAG）模式在讓人工智慧更具知識性與可靠性上邁出重大一步。透過在生成流程中無縫加入外部知識檢索步驟，RAG 解決了單一 LLM 的核心限制。嵌入與語意相似度等基礎概念，加上關鍵字搜尋與混合搜尋等檢索技術，讓系統能智慧地找到相關資訊，並透過策略性的切塊使資訊更易於處理。整個檢索流程依賴專門的向量資料庫，以規模化方式儲存並有效查詢數以百萬計的嵌入。儘管在檢索分散或互斥資訊時仍面臨挑戰，RAG 已是打造可靠 AI 代理不可或缺的一環。

## RAG 與代理的進階層（Agentic RAG）

透過代理方法組織的 RAG 系統，不再僅是被動地拉取資料，而是讓智慧代理在檢索流程中扮演積極角色。代理會運用推理（reasoning）、規劃（planning）與工具使用（tool use）來確保輸出內容可信且扎實。這種方法特別適合需要多步推理、檢查資料一致性與整合多個來源的工作。

### 代理在 RAG 中的角色（Role of the Agent in RAG）

首先，代理可以扮演策展人的角色，評估並選擇最適合的資訊來源。例如，代理可能先評估內部維基或客戶支援資料庫的可信度，再決定要檢索哪一個資料庫。這種策展步驟避免了檢索無關或品質低落的資料。其次，代理能將複雜問題拆解為可管理的子問題，對每個子問題分別進行檢索與推理，最後再彙整答案。第三，代理能對從不同來源取得的資訊進行交叉驗證，若發現內容衝突，會進一步檢視、加註不確定性或請求更多資料。第四，代理能識別知識缺口並使用外部工具。假設使用者詢問「我們昨天推出的新產品，市場即時反應如何？」代理在每週更新的內部知識庫中找不到相關資訊，便會偵測這個缺口，啟用工具（例如即時網路搜尋 API），找到最新的新聞稿與社群媒體聲量，再把這些資訊整合成即時回應，克服靜態內部資料庫的限制。

### 代理式 RAG 的挑戰（Challenges of Agentic RAG）

雖然功能強大，代理層也帶來一系列挑戰。首要缺點是複雜度與成本大幅增加。設計、實作與維護代理的決策邏輯與工具整合需要大量工程投入，也會提高計算成本。這種複雜度同時可能導致延遲增加，因為代理在反思、使用工具及多步推理上的循環，所耗時間遠超過標準的直接檢索流程。此外，代理本身也可能成為新的錯誤來源；若推理過程有瑕疵，就可能陷入無用的迴圈、誤解任務或錯誤捨棄重要資訊，最終降低回應品質。

### 總結（In Summary）

代理式 RAG 是標準檢索模式的精進版，將其從被動的資料管線轉變為主動的問題解決框架。透過嵌入能評估來源、調和衝突、拆解複雜問題並使用外部工具的推理層，代理大幅提升生成答案的可信度與深度。這項進步讓 AI 更值得信賴且功能更強大，但同時伴隨系統複雜度、延遲與成本等必須謹慎管理的重大權衡。

## 實務應用與使用案例（Practical Applications & Use Cases）

知識檢索（RAG）正在改變 LLM 在各行各業的應用方式，讓它們能提供更準確且更符合情境的回應。

應用情境包括：

* **企業搜尋與問答（Enterprise Search and Q&A）：** 組織可以打造內部聊天機器人，利用公司內部文件（如人資政策、技術手冊、產品規格）回覆員工詢問。RAG 系統會擷取相關段落來支援 LLM 的回答。
* **客戶支援與服務台（Customer Support and Helpdesks）：** 建基於 RAG 的系統可透過存取產品手冊、常見問題（FAQ）與支援單來提供精準且一致的答覆，減少人力處理例行問題的需求。
* **個人化內容推薦（Personalized Content Recommendation）：** RAG 不再只靠關鍵字配對，而是能找出與使用者喜好或過往互動在語意上相關的內容（文章、產品），提供更貼切的建議。
* **新聞與即時事件摘要（News and Current Events Summarization）：** LLM 可以整合即時新聞來源。當使用者詢問當前事件時，RAG 系統會檢索最新文章，使 LLM 能給出最新的摘要。

透過整合外部知識，RAG 讓 LLM 的能力超越單純溝通，進而成為知識處理系統。

## 實作範例（Hands-On Code Example，ADK）

為了說明知識檢索（RAG）模式，以下提供三個範例。

首先示範如何利用 Google 搜尋執行 RAG，並將 LLM 的回答鍊接到搜尋結果。由於 RAG 牽涉到存取外部資訊，Google 搜尋工具就是直接強化 LLM 知識的內建檢索機制範例。

```python
from google.adk.tools import google_search
from google.adk.agents import Agent


search_agent = Agent(
    name="research_assistant",
    model="gemini-2.0-flash-exp",
    instruction="You help users research topics. When asked, use the Google Search tool",
    tools=[google_search],
)
```

第二部分說明如何在 Google ADK 中使用 Vertex AI RAG 功能。範例程式示範如何初始化 ADK 的 `VertexAiRagMemoryService`，藉此建立與 Google Cloud Vertex AI RAG Corpus 的連線。服務透過設定資料集資源名稱與 `SIMILARITY_TOP_K`、`VECTOR_DISTANCE_THRESHOLD` 等選用參數來影響檢索流程。`SIMILARITY_TOP_K` 決定要擷取的最相似結果數量，`VECTOR_DISTANCE_THRESHOLD` 則設定語意距離上限。此設定讓代理可以從指定的 RAG Corpus 執行具規模且持久的語意知識檢索，將 Google Cloud 的 RAG 功能整合進 ADK 代理，支援生成以事實為基礎的回應。

```python
# Import the necessary VertexAiRagMemoryService class from the google.adk.memory module.
from google.adk.memory import VertexAiRagMemoryService


RAG_CORPUS_RESOURCE_NAME = "projects/your-gcp-project-id/locations/us-central1/ragCorpora/your-corpus-id"

# Define an optional parameter for the number of top similar results to retrieve.
# This controls how many relevant document chunks the RAG service will return.
SIMILARITY_TOP_K = 5

# Define an optional parameter for the vector distance threshold.
# This threshold determines the maximum semantic distance allowed for retrieved results;
# results with a distance greater than this value might be filtered out.
VECTOR_DISTANCE_THRESHOLD = 0.7

# Initialize an instance of VertexAiRagMemoryService.
# This sets up the connection to your Vertex AI RAG Corpus.
# - rag_corpus: Specifies the unique identifier for your RAG Corpus.
# - similarity_top_k: Sets the maximum number of similar results to fetch.
# - vector_distance_threshold: Defines the similarity threshold for filtering results.
memory_service = VertexAiRagMemoryService(
    rag_corpus=RAG_CORPUS_RESOURCE_NAME,
    similarity_top_k=SIMILARITY_TOP_K,
    vector_distance_threshold=VECTOR_DISTANCE_THRESHOLD,
)
```

## 實作範例（Hands-On Code Example，LangChain）

第三部分帶領讀者透過 LangChain 完成一個完整範例。

```python
import os
import requests
from typing import List, Dict, Any, TypedDict

from langchain_community.document_loaders import TextLoader
from langchain_core.documents import Document
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_community.embeddings import OpenAIEmbeddings
from langchain_community.vectorstores import Weaviate
from langchain_openai import ChatOpenAI
from langchain.text_splitter import CharacterTextSplitter
from langchain.schema.runnable import RunnablePassthrough
from langgraph.graph import StateGraph, END

import weaviate
from weaviate.embedded import EmbeddedOptions
import dotenv


# Load environment variables (e.g., OPENAI_API_KEY)
dotenv.load_dotenv()

# Set your OpenAI API key (ensure it's loaded from .env or set here)
# os.environ["OPENAI_API_KEY"] = "YOUR_OPENAI_API_KEY"


# --- 1. Data Preparation (Preprocessing) ---

# Load data
url = "https://github.com/langchain-ai/langchain/blob/master/docs/docs/how_to/state_of_the_union.txt"
res = requests.get(url)
with open("state_of_the_union.txt", "w") as f:
    f.write(res.text)

loader = TextLoader("./state_of_the_union.txt")
documents = loader.load()

# Chunk documents
text_splitter = CharacterTextSplitter(chunk_size=500, chunk_overlap=50)
chunks = text_splitter.split_documents(documents)

# Embed and store chunks in Weaviate
client = weaviate.Client(embedded_options=EmbeddedOptions())

vectorstore = Weaviate.from_documents(
    client=client,
    documents=chunks,
    embedding=OpenAIEmbeddings(),
    by_text=False,
)

# Define the retriever
retriever = vectorstore.as_retriever()

# Initialize LLM
llm = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0)


# --- 2. Define the State for LangGraph ---
class RAGGraphState(TypedDict):
    question: str
    documents: List[Document]
    generation: str


# --- 3. Define the Nodes (Functions) ---
def retrieve_documents_node(state: RAGGraphState) -> RAGGraphState:
    """Retrieves documents based on the user's question."""
    question = state["question"]
    documents = retriever.invoke(question)
    return {"documents": documents, "question": question, "generation": ""}


def generate_response_node(state: RAGGraphState) -> RAGGraphState:
    """Generates a response using the LLM based on retrieved documents."""
    question = state["question"]
    documents = state["documents"]

    # Prompt template from the PDF
    template = """You are an assistant for question-answering tasks. Use the following pieces of retrieved context to answer the question. If you don't know the answer, just say that you don't know. Use three sentences maximum and keep the answer concise.
Question: {question}
Context: {context}
Answer: """
    prompt = ChatPromptTemplate.from_template(template)

    # Format the context from the documents
    context = "\n\n".join([doc.page_content for doc in documents])

    # Create the RAG chain
    rag_chain = prompt | llm | StrOutputParser()

    # Invoke the chain
    generation = rag_chain.invoke({"context": context, "question": question})

    return {"question": question, "documents": documents, "generation": generation}


# --- 4. Build the LangGraph Graph ---
workflow = StateGraph(RAGGraphState)

# Add nodes
workflow.add_node("retrieve", retrieve_documents_node)
workflow.add_node("generate", generate_response_node)

# Set the entry point
workflow.set_entry_point("retrieve")

# Add edges (transitions)
workflow.add_edge("retrieve", "generate")
workflow.add_edge("generate", END)

# Compile the graph
app = workflow.compile()


# --- 5. Run the RAG Application ---
if __name__ == "__main__":
    print("\n--- Running RAG Query ---")
    query = "What did the president say about Justice Breyer"
    inputs = {"question": query}
    for s in app.stream(inputs):
        print(s)

    print("\n--- Running another RAG Query ---")
    query_2 = "What did the president say about the economy?"
    inputs_2 = {"question": query_2}
    for s in app.stream(inputs_2):
        print(s)
```

這段 Python 程式碼展示了使用 LangChain 與 LangGraph 實作的檢索增強生成（RAG）流程。首先，系統從文本文件建立知識庫，將其切割成區塊並轉換為嵌入，再儲存在 Weaviate 向量儲存（vector store）中以利高效檢索。LangGraph 的狀態圖（StateGraph）用來管理兩個核心函式之間的工作流程：`retrieve_documents_node` 與 `generate_response_node`。`retrieve_documents_node` 會依照使用者輸入向向量儲存查詢相關文件區塊，接著 `generate_response_node` 利用取得的資訊與預先定義的提示模板，透過 OpenAI 的 LLM 產生回應。`app.stream` 方法展示如何透過 RAG 流程執行查詢，凸顯系統生成符合情境的輸出能力。

## 重點一覽(At a Glance)

**重點是什麼（What）：** LLM 雖然具備令人讚嘆的文本生成能力，但根本限制在於其訓練資料是靜態的，缺乏即時資訊或私有領域知識。因此，它們的回應可能過時、不精確，或缺乏專業任務所需的特定脈絡，限制了在需要最新且基於事實的回覆時的可靠度。

**為何需要（Why）：** 檢索增強生成（RAG）提供標準化的解決方案，將 LLM 連結到外部知識來源。當系統收到查詢時，會先從指定的知識庫檢索相關片段，再把這些片段附加到原始提示，補充即時且具體的脈絡。這份增強提示隨後送進 LLM，使其生成準確、可驗證且紮根於外部資料的回應。此流程有效將 LLM 從閉卷推理者轉變為開卷推理者，顯著提升其實用性與可信度。

**經驗法則（Rule of Thumb）：** 當你需要 LLM 針對原始訓練資料之外的最新或專有資訊回答問題或產生內容時，就應使用此模式。它特別適合建立內部文件的問答系統、客戶支援機器人，以及需要可驗證、具引用的事實性回應的應用。

**視覺摘要（Visual Summary）：**

![Knowledge Retrieval Pattern Database](../assets/Knowledge_Retrieval_Pattern_Database.png)

知識檢索模式：AI 代理查詢並自結構化資料庫檢索資訊

![Knowledge Retrieval Pattern Search](../assets/Knowledge_Retrieval_Pattern_Search.png)

圖 3：知識檢索模式：AI 代理針對使用者查詢從公共網際網路尋找並綜整資訊。

## 重要重點（Key Takeaways）

* 知識檢索（RAG）透過讓 LLM 存取外部、最新且特定的資訊來強化其能力。
* 此流程包含檢索（Retrieval，從知識庫尋找相關片段）與增強（Augmentation，把片段附加到 LLM 提示）。
* RAG 有助於 LLM 克服舊資料的限制，減少「幻覺」，並整合領域專屬知識。
* RAG 讓答案可追溯來源，因為 LLM 的回應立基於檢索到的資訊。
* GraphRAG 利用知識圖譜（knowledge graph）理解資訊之間的關聯，可回答需要整合多重來源的複雜問題。
* 代理式 RAG 透過智慧代理主動推理、驗證與修正外部知識，確保答案更精準可靠。
* 實務應用涵蓋企業搜尋、客戶支援、法律研究與個人化推薦等領域。

## 結論（Conclusion）

總而言之，檢索增強生成（RAG）透過連結外部、最新的資料來源，解決了 LLM 靜態知識的根本限制。流程先檢索相關資訊片段，再增強使用者提示，使 LLM 能生成更準確且具情境意識的回應。這一切仰賴嵌入、語意搜尋與向量資料庫等基礎技術，讓系統能根據意義而非僅憑關鍵字尋找資訊。藉由以可驗證資料作為支撐，RAG 大幅降低事實錯誤，並允許使用專有資訊，同時透過引用提升信任度。

進一步的發展如代理式 RAG，引入能主動驗證、調和與綜整檢索知識的推理層，使系統更可靠。同樣地，GraphRAG 等專門方法運用知識圖譜來探索資料間的明確關聯，可處理高度複雜且互相連結的查詢。這類代理可以化解矛盾資訊、執行多步查詢並使用外部工具尋找缺漏。儘管這些進階方法增加了複雜度與延遲，卻大幅提升最終回應的深度與可信度。這些模式的實務應用已在改變各產業，從企業搜尋、客戶支援到個人化內容傳遞。儘管仍有挑戰，RAG 是讓 AI 更具知識性、可信與實用的關鍵模式，最終讓 LLM 從閉卷的對話者蛻變為強大的開卷推理工具。

## References

1. Lewis, P., et al. (2020). *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. [https://arxiv.org/abs/2005.11401](https://arxiv.org/abs/2005.11401)
2. Google AI for Developers Documentation.  *Retrieval Augmented Generation - [https://cloud.google.com/vertex-ai/generative-ai/docs/rag-engine/rag-overview](https://cloud.google.com/vertex-ai/generative-ai/docs/rag-engine/rag-overview)*
3. Retrieval-Augmented Generation with Graphs (GraphRAG), [https://arxiv.org/abs/2501.00309](https://arxiv.org/abs/2501.00309)
4. LangChain and LangGraph: Leonie Monigatti, "Retrieval-Augmented Generation (RAG): From Theory to LangChain Implementation," [*https://medium.com/data-science/retrieval-augmented-generation-rag-from-theory-to-langchain-implementation-4e9bd5f6a4f2*](https://medium.com/data-science/retrieval-augmented-generation-rag-from-theory-to-langchain-implementation-4e9bd5f6a4f2)
5. Google Cloud Vertex AI RAG Corpus [*https://cloud.google.com/vertex-ai/generative-ai/docs/rag-engine/manage-your-rag-corpus#corpus-management*](https://cloud.google.com/vertex-ai/generative-ai/docs/rag-engine/manage-your-rag-corpus#corpus-management)
