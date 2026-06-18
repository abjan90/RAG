# 📄 RAG Pipeline for PDF Document Q&A

A Retrieval-Augmented Generation (RAG) pipeline that ingests PDF documents, stores them in a vector database, and answers natural language questions using semantic search and an LLM.

---

## 🧠 How It Works

```
PDF Documents → Chunking → Embeddings → ChromaDB Vector Store
                                                   ↓
                          User Query → Semantic Retrieval → LLM → Answer
```

1. **Document Loading** — PDFs are loaded recursively from a directory using `PyMuPDFLoader`
2. **Chunking** — Documents are split into overlapping chunks using `RecursiveCharacterTextSplitter`
3. **Embedding** — Chunks are embedded using the `all-MiniLM-L6-v2` sentence transformer model
4. **Vector Store** — Embeddings are stored and persisted in ChromaDB
5. **Retrieval** — On query, the top-k most semantically similar chunks are retrieved
6. **Generation** — Retrieved context is passed to a Groq-hosted LLM to generate the final answer

---

## 🗂️ Project Structure

```
rag-pipeline/
├── textfile/               # Place your PDF files here
├── vector_store/           # ChromaDB persistent storage (auto-created)
├── rag_pipe.ipynb          # Main pipeline notebook
├── .env                    # API keys (not committed)
├── requirements.txt
└── README.md
```

---

## ⚙️ Setup

### 1. Clone the repository

```bash
git clone https://github.com/your-username/rag-pipeline.git
cd rag-pipeline
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure environment variables

Create a `.env` file in the root directory:

```env
GROQ_API_KEY=your_groq_api_key_here
```

> Get your free Groq API key at [console.groq.com](https://console.groq.com)

### 4. Add your PDFs

Place your PDF files inside the `textfile/` directory. Subdirectories are supported.

---

## 🚀 Usage

Open and run `rag_pipe.ipynb` top to bottom. The notebook is organized into four stages:

| Stage | Description |
|-------|-------------|
| **1. Document Loading** | Loads all PDFs from `textfile/` |
| **2. Chunking** | Splits documents into 1000-char chunks with 200-char overlap |
| **3. Embedding & Indexing** | Generates embeddings and stores them in ChromaDB |
| **4. Query & Answer** | Ask any question and get a context-grounded answer |

To ask a question after setup:

```python
answer = rag_simple("What is wastewater?", rag_retreiver, llm)
print(answer)
```

---

## 🛠️ Tech Stack

| Component | Library |
|-----------|---------|
| PDF Loading | `langchain-community` + `PyMuPDF` |
| Text Splitting | `langchain-text-splitters` |
| Embeddings | `sentence-transformers` (`all-MiniLM-L6-v2`) |
| Vector Store | `chromadb` |
| LLM | `langchain-groq` (`llama-3.3-70b-versatile`) |
| Environment | `python-dotenv` |

---

## 📦 Requirements

```txt
langchain-community
langchain-text-splitters
langchain-groq
langchain-core
pymupdf
sentence-transformers
chromadb
scikit-learn
numpy
python-dotenv
```

Install all at once:

```bash
pip install langchain-community langchain-text-splitters langchain-groq langchain-core pymupdf sentence-transformers chromadb scikit-learn numpy python-dotenv
```

---

## ⚠️ Known Issues

- **Duplicate retrieval results** — Running the notebook multiple times without clearing the vector store will add duplicate embeddings to ChromaDB. To reset:

  ```python
  import shutil
  shutil.rmtree("vector_store")
  ```

- **Low similarity scores** — Short or generic queries may return low-confidence chunks. Adjust `similarity_threshold` in the `Retriever` class as needed.

---

## 🔧 Configuration

Key parameters you can tune:

| Parameter | Location | Default | Description |
|-----------|----------|---------|-------------|
| `chunk_size` | `spli_documents()` | `1000` | Characters per chunk |
| `chunk_overlap` | `spli_documents()` | `200` | Overlap between chunks |
| `top_k` | `retrieve()` | `5` | Number of chunks to retrieve |
| `similarity_threshold` | `retrieve()` | `0.0` | Minimum similarity score to include |
| `temperature` | `GroqLLM` | `0.1` | LLM response randomness |
| `max_tokens` | `GroqLLM` | `1024` | Maximum response length |

---

## 📝 License

MIT License — feel free to use and modify for your own projects.
