# SEC Filing Query Engine with RAG and Gemini

📈 This repository contains the code for a powerful **Retrieval-Augmented Generation (RAG)** pipeline designed to answer natural language questions about public company SEC filings. By leveraging modern NLP and LLM technologies, this project transforms dense, unstructured financial documents into an interactive and queryable knowledge base.

This notebook serves as a practical, end-to-end example of building a sophisticated Q&A system for domain-specific documents.

---

## Notebook

The main implementation is contained within the `rag-pipeline-for-sec-filings.ipynb` notebook. You can also view and run this notebook directly on Kaggle.

[![Kaggle](https://kaggle.com/static/images/open-in-kaggle.svg)](https://www.kaggle.com/code/snehdadhania/rag-pipeline-for-sec-filings-queries) <!-- <<<--- INSERT YOUR KAGGLE LINK HERE -->

---

## 🤖 Key Features

-   **Automated Data Ingestion**: Downloads the latest SEC filings (10-K, 10-Q, 8-K, etc.) for multiple tickers within a specified date range.
-   **Intelligent Document Parsing**: Uses the `sec-parser` library to semantically segment filings into meaningful sections (e.g., "Item 1. Business", "Item 7. Management's Discussion and Analysis").
-   **Vector-Based Retrieval**: Chunks the parsed text, generates vector embeddings using `sentence-transformers`, and stores them in a persistent **ChromaDB** vector database.
-   **Retrieval-Augmented Generation (RAG)**: When a user asks a question, the system retrieves the most relevant text chunks from the filings to use as context.
-   **LLM-Powered Answers**: Feeds the user's query and the retrieved context into **Google's Gemini Pro** to generate accurate, concise, and context-aware answers.
-   **Metadata Richness**: Each stored chunk includes metadata like the ticker, form type, filing date, and section title, which is used for citation and context grounding.

---

## 🔧 How It Works: The RAG Pipeline

The project is implemented in two distinct phases within the notebook: the **Indexing Pipeline** and the **Querying Pipeline**.

### Phase 1: Indexing Pipeline (Data Ingestion and Processing)

This phase builds the knowledge base by processing the SEC filings. This is handled in **Cell A** of the notebook.
[Download Filings] -> [Parse into Sections] -> [Chunk Text] -> [Create Embeddings] -> [Store in Vector DB]
code
Code
1.  **Download Filings**: Fetches specified forms (e.g., 10-Q, 10-K) for a list of tickers (`AAPL`, `MSFT`) from the SEC EDGAR database.
2.  **Parse into Sections**: The raw HTML of each filing is parsed into structured, semantic sections (like "Item 1A. Risk Factors").
3.  **Chunk Text**: Each section's text is broken down into smaller, manageable chunks suitable for embedding.
4.  **Create Embeddings**: The `sentence-transformers` model converts each text chunk into a high-dimensional vector representation.
5.  **Store in Vector DB**: The chunks, their embeddings, and associated metadata are stored (upserted) into a persistent ChromaDB collection.

### Phase 2: Querying Pipeline (Answering Questions)

This phase handles user queries by retrieving relevant information and generating an answer. This is handled in **Cell B** of the notebook.
[User Query] -> [Embed Query] -> [Retrieve Context] -> [Augment Prompt] -> [Generate Answer with LLM]
code
Code
1.  **Embed Query**: The user's natural language question is converted into a vector embedding using the same model.
2.  **Retrieve Context**: The query embedding is used to perform a similarity search in ChromaDB, retrieving the `top_k` most relevant text chunks from the filings.
3.  **Augment Prompt**: The retrieved chunks are combined with the original query into a detailed prompt for the LLM. This prompt instructs the model to act as a financial assistant and answer based only on the provided context.
4.  **Generate Answer**: The augmented prompt is sent to the Gemini LLM, which synthesizes the information in the context to generate a final, human-readable answer.

---

## 🛠️ Technology Stack

-   **Data Retrieval & Parsing**: `sec-downloader`, `sec-parser`, `requests`, `beautifulsoup4`
-   **Vector Embeddings**: `sentence-transformers`
-   **Vector Database**: `ChromaDB`
-   **Language Model (LLM)**: `google-generativeai` (Gemini Pro)
-   **Core Libraries**: `numpy`, `pandas`, `jupyter`

---

## 🚀 Setup and Installation

To run this project, you will need Python 3 and the necessary libraries. It is recommended to use a virtual environment.

1.  **Clone the repository:**
    ```bash
    git clone <repository-url>
    cd <repository-directory>
    ```

2.  **Install Dependencies**:
    The notebook includes a `pip install` command in the second cell to install all required packages.

3.  **Set up Google API Key**:
    You will need a Google API key to use the Gemini model.
    -   Obtain a key from [Google AI Studio](https://aistudio.google.com/app/apikey).
    -   In the notebook, replace the placeholder in the third cell with your key:
        ```python
        # In rag-pipeline-for-sec-filings.ipynb
        GOOGLE_API_KEY="YOUR_GOOGLE_API_KEY_HERE"
        ```

## 💻 How to Run

1.  **Launch Jupyter Notebook or JupyterLab**:
    ```bash
    jupyter notebook
    ```

2.  **Open the Notebook**: Open the `rag-pipeline-for-sec-filings.ipynb` file.

3.  **Run the Indexing Pipeline**:
    -   Execute **Cell A** (`==== CELL A: INDEX ====`).
    -   This will download the filings and build the ChromaDB vector store in a local directory (`./chroma_sec_multi`). This step may take some time depending on the number of filings.

4.  **Run the Querying Pipeline**:
    -   Once the indexing is complete, you can run **Cell B** (`==== CELL B: QUERY ====`).
    -   Modify the `user_query` variable in this cell to ask your own questions about the indexed filings.
    -   The cell will print the retrieved context (Top hits) and the final answer generated by Gemini.

