# COMP 3610 Assignment 3: LLM-Powered Applications & Distributed Computing

## Project Description

This project implements an integrated intelligent data analytics system that combines
Apache Spark for distributed data processing with a Retrieval-Augmented Generation
(RAG) pipeline for document-based question answering. The system provides a unified
natural language interface that routes user queries to the appropriate backend —
structured SQL analytics, document retrieval, or a hybrid of both.

**Dataset:** NYC Yellow Taxi Trip Records (January 2024)  
**Document Corpus:** NYC TLC policy documents (stored in `docs/`)

---

## Project Structure
```
COMP3610-Assignment3/
│
├── assignment3.ipynb        # Main Jupyter notebook (all code + outputs)
├── docs/                    # PDF policy document corpus
│   ├── annual_report_2023.pdf
│   ├── strategic_plan_2025.pdf
│   ├── fhv_wheelchair_accessibility_report_2020.pdf
│   ├── ssrn-4760839.pdf
│   └── emptyseatsfullstreets.pdf
├── requirements.txt         # Python dependencies
├── README.md                # This file
└── .gitignore               # Excludes data files, chroma_db/, __pycache__/
```

> **Note:** The NYC Taxi `.parquet` data file and `chroma_db/` vector database are
> excluded from this repository. The notebook downloads the data automatically on first run.

---

## Setup Instructions

### Prerequisites
- Python 3.9+
- Java 8, 11, or 17 (required by PySpark)
  - Linux: `sudo apt install default-jdk`
  - macOS: `brew install openjdk`
  - Verify: `java -version`
- Google Colab (recommended) or a local Jupyter environment

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/COMP3610-Assignment3.git
cd COMP3610-Assignment3
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. Run the Notebook

Open `assignment3.ipynb` in Jupyter or Google Colab and run all cells in order.
The first cell will automatically download the NYC Taxi dataset (~50MB).

> If using Google Colab, Java is pre-installed and no additional setup is needed.

---

## System Components

### Part 1 — Spark Data Engineering
- SparkSession configured with Adaptive Query Execution (AQE)
- Data cleaning pipeline with logged row counts at each step
- Feature engineering: `trip_duration_minutes`, `trip_speed_mph`, `pickup_hour`,
  `pickup_day_of_week`, `tip_percentage`
- Spark SQL analytics with window functions
- Performance optimization: caching, partitioned Parquet write, execution plan analysis

### Part 2 — RAG Pipeline
- PDF corpus loaded via LangChain `PyPDFDirectoryLoader`
- Text chunked with `RecursiveCharacterTextSplitter` (chunk_size=1000, overlap=200)
- Embeddings generated using `sentence-transformers/all-MiniLM-L6-v2`
- Vector store persisted to ChromaDB
- Full RAG pipeline: retrieve → format context → augment prompt → generate answer
- Evaluation on 10 manually verified question-answer pairs

### Part 3 — Integrated Application
- LLM-powered query router classifying queries as DATA, DOCUMENT, or HYBRID
- Natural language to Spark SQL translation with error-handling retry logic
- End-to-end assistant combining both backends for hybrid queries

---

## AI Assistance Disclosure

AI tools (Claude by Anthropic) were used during development in the following ways:

- **Code review & debugging:** Claude identified a bug in the `execute_data_query`
  retry logic where the error context was being discarded on the recursive call.
- **Dependency error interpretation:** Claude confirmed that pip dependency conflict
  warnings (opentelemetry, google-adk) were non-critical and could be ignored.
- **Assignment review:** Claude was used to cross-reference the submission against
  the marking criteria and identify gaps prior to submission.

All code was reviewed and understood before inclusion. The core implementation was
written independently.

---

## Notes

- Random seed `42` is used where reproducibility is required
- Do not commit `chroma_db/`, `*.parquet`, or `__pycache__/` (covered by `.gitignore`)
- The LLM API endpoint used is the course-provided Llama 3.3 70B server
