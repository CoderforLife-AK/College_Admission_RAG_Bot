# College Admissions AI Agent (RAG Pipeline)

An advanced Retrieval-Augmented Generation (RAG) assistant designed to automate complex college admission queries by referencing structured institutional guidelines. Built using **Langflow** and powered natively by **IBM watsonx.ai** models and **FAISS**.

---

## 🛠️ Detailed System Architecture & Workflow Structure

The system is engineered as an asynchronous, state-managed pipeline that transforms unstructured institutional rules into high-fidelity context streams for a Large Language Model. Below is the end-to-end data processing workflow:

### Phase 1: Data Ingestion & Tokenization
1. **Source Loading**: The unstructured document (`admission_guidelines.txt`) containing eligibility grids, mandatory checklists, and unified fee data is fed into a file ingestion node.
2. **Recursive Character Text Splitting**: To ensure semantic integrity, the text is split into chunks using a **Chunk Size of 1200 tokens** and a **Chunk Overlap of 200 tokens**. It targets paragraph structures (`\n\n`) first, preventing critical values (like the INR 1,20,000 tuition fee) from being sliced across chunk boundaries.

### Phase 2: Vector Embedding & Index Generation
3. **IBM Slate Representation**: The text chunks are processed via the **IBM watsonx.ai Embeddings** block using the `ibm/slate-125m-english-rtrvr-v2` model (part of the **IBM Granite** family). This translates text strings into high-dimensional vector embeddings.
4. **FAISS Vector Storage**: The generated embeddings are indexed locally into a **FAISS (Facebook AI Similarity Search)** database. The parameter `allow_dangerous_deserialization` is enabled to allow rapid, local reading of the compressed index matrix.

### Phase 3: Semantic Query & Context Parsing
5. **Runtime User Query**: The user asks a question via the Langflow Playground (e.g., *"What are the hostel fees for girls?"*).
6. **Vector Search (K=5)**: The user's question is converted into an embedding using the same Slate model. FAISS conducts a cosine similarity search and retrieves the top **5 most relevant chunks (K=5)** to ensure no deep information (like Section 4 administrative data) is missed.
7. **Stringify Data Parsing**: The incoming list of array objects from FAISS is passed to a **Parser Block** configured to **Stringify** mode. This flattens the multiple individual context segments into a single, seamless plain text block.

### Phase 4: Prompt Engineering & Generation
8. **Context Injection**: The flattened text string is dynamically injected into the `{context}` variable of the Prompt Template block, alongside the user's raw query (`{question}`).
9. **Inference Execution**: The finalized instruction payload is sent to the **IBM watsonx.ai LLM Engine**. The model processes the prompt context and outputs a deterministic, hallucination-free answer strictly matching the official text guidelines.

---

## 📁 Repository Directory Layout

```text
📁 College-Admissions-RAG-Bot/
├── Final_Project.json                <-- Sanitized Langflow JSON layout template
├── College_Admission_policy.docx     <-- Structured institutional guidelines dataset
├── .gitignore                        <-- Directives preventing tracking of local keys
├── requirements.txt                  <-- Global Python environment dependencies
└── README.md                         <-- Complete architectural specification and manual
