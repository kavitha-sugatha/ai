# RAG - Retrieval Augmented Generation for Health Insurance Policy Intelligence

## Overview

This project is a Retrieval-Augmented Generation (RAG) system designed for MeridianHealth Insurance, a mid-sized health insurance carrier serving approximately 2.4 million covered lives across 22 states. The system enables sales representatives to retrieve accurate, source-grounded answers from health insurance documentation in real-time during live customer conversations.

## Business Context

MeridianHealth's sales representatives face significant challenges when customers ask specific policy questions during live conversations:

- **Information scattering**: Answers are buried across 47+ active plan designs spanning thousands of pages across policy brochures, terms & conditions, claim exclusions, and claim guides
- **New representative onboarding**: 35% of the field force are new representatives who often don't know where answers live
- **Experienced representative struggles**: Even experienced reps struggle with multi-document questions, waiting periods, exclusions, and conditional coverage rules
- **Current workarounds**: Calling the helpdesk (11,000-13,000 queries/month), putting customers on hold, or guessing — each carrying significant costs

**Impact metrics:**
- 18-22% of sales conversations stall at the question-handling stage
- Misrepresentation complaints leading to substantial remediation costs
- New reps take 9-12 months to reach full productivity
- Senior specialists spend large portions of time answering repetitive field queries

## Objective

Build a RAG-based policy intelligence system that:

1. **Ingests** policy brochures, terms & conditions, claim exclusions, and claim guides into a searchable vector database
2. **Retrieves** relevant policy context when a sales representative asks a question
3. **Generates** precise, grounded answers using a Large Language Model
4. **Returns supporting source passages** and document references alongside every answer for verification
5. **Handles comparative and cross-document questions** (differences between benefits/exclusions, waiting periods, combined reimbursement/cashless guidance)
6. **Includes prompt optimization and evaluation** using DeepEval and GEPA-based prompt tuning
7. **Evaluates multiple RAG configurations** (retrieval depth, generation parameters) for production readiness

## Data Documents

The system uses four health insurance documents stored locally:

| Document | Description |
|----------|-------------|
| `Policy_Brocher.pdf` | Main product overview in simple language including family floater plan details, benefits, features, eligibility, claims, exclusions, renewals, member addition rules, and premium factors |
| `Health_Insurance_Policy_TandC.pdf` | Formal policy wording including definitions, benefits covered, general conditions, waiting periods, policy rules, claim conditions, renewal terms, and legal policy clauses |
| `List_of_Standard_Claim_Exclusions.pdf` | Items and charges not payable under standard health claims, plus items only payable under specific policy wording; hospital-service and room-charge items that cannot be billed separately |
| `Health_Insurance_Claim_Guide.pdf` | Reimbursement and cashless claim process, document requirements, claim status steps, and common FAQ guidance for policyholders |

## Setup

### Prerequisites

- Python 3.14+
- OpenAI API key
- Access to the RAG directory with PDF documents and benchmark dataset

### Installation

```bash
# Install required libraries
pip install chromadb==1.5.9 langchain-community==0.4.1 langchain-chroma==1.1.0 \
  langchain-openai==1.2.1 langchain-text-splitters==1.1.2 \
  pandas>=2.2.0 numpy>=1.26.0 scikit-learn>=1.4.0 \
  python-dotenv>=1.0.1 tqdm>=4.66.0 pypdf>=5.0.0 deepeval
```

### Configuration

1. Create a `config.json` file with your OpenAI credentials:
```json
{
  "OPENAI_API_KEY": "your-api-key",
  "OPENAI_API_BASE": "https://api.openai.com/v1"
}
```

2. Ensure the following files are in the `RAG/` directory:
   - `Policy_Brocher.pdf`
   - `Health_Insurance_Policy_TandC.pdf`
   - `List_of_Standard_Claim_Exclusions.pdf`
   - `Health_Insurance_Claim_Guide.pdf`
   - `golden_benchmark_dataset.csv`

## Architecture

### Pipeline Overview

1. **Document Loading**: PDFs are loaded using `PyPDFLoader`, with each page becoming a LangChain Document
2. **Text Chunking**: Documents are split using `RecursiveCharacterTextSplitter` (chunk_size=1200, chunk_overlap=150) to preserve context continuity
3. **Vector Store**: Chunked documents are embedded using `OpenAIEmbeddings` (text-embedding-3-small) and indexed into a local `Chroma` vector store
4. **Retrieval**: Similarity search retrieves the most relevant k chunks for a given question
5. **Context Formatting**: Retrieved documents are formatted into a compact context block with source metadata
6. **Answer Generation**: A `ChatOpenAI` model (gpt-4o-mini) generates answers using baseline prompts
7. **Evaluation**: Answers are evaluated using DeepEval metrics (relevance, faithfulness, completeness)
8. **Prompt Optimization**: GEPA-based prompt optimization improves answer quality iteratively

### Key Components

- **`load_pdf_documents()`**: Loads PDFs and adds source-level metadata
- **`format_docs()`**: Converts retrieved documents into prompt-ready context blocks
- **`retrieve_docs()`**: Performs similarity search against the Chroma vector store
- **`baseline_answer()`**: End-to-end RAG workflow (retrieve → format → generate)
- **`load_gold_benchmark()`**: Loads the evaluation dataset with required columns (question, answer, context, supporting_sources)
- **Prompt templates**: Baseline system prompt and user prompt for answer generation

## RAG Pipeline Steps

### Step 1: Load PDF Documents
```python
brochure_docs = load_pdf_documents(POLICY_BROCHURE_PDF)
terms_docs = load_pdf_documents(POLICY_TERMS_PDF)
exclusions_docs = load_pdf_documents(CLAIM_EXCLUSIONS_PDF)
claim_guide_docs = load_pdf_documents(CLAIM_GUIDE_PDF)
```

### Step 2: Split into Chunks
```python
all_source_docs = brochure_docs + terms_docs + exclusions_docs + claim_guide_docs
splitter = RecursiveCharacterTextSplitter(chunk_size=1200, chunk_overlap=150)
policy_chunks = splitter.split_documents(all_source_docs)
# Result: 195 chunks from 70 source documents
```

### Step 3: Build Chroma Vector Store
```python
embeddings = OpenAIEmbeddings(model="text-embedding-3-small")
vectorstore = Chroma.from_documents(
    documents=policy_chunks,
    embedding=embeddings,
    collection_name="health_policy_intelligence",
    persist_directory=str(CHROMA_DIR)
)
# Indexed documents: 195
```

### Step 4: Retrieve and Generate Answers
```python
def baseline_answer(question, k=4):
    docs = retrieve_docs(question, k=k)
    context = format_docs(docs)
    user_prompt = BASELINE_USER_PROMPT.format(question=question, context=context)
    response = llm.invoke([
        SystemMessage(content=BASELINE_SYSTEM_PROMPT),
        HumanMessage(content=user_prompt)
    ])
    return {
        "answer": response.content,
        "docs": docs,
        "context": context
    }
```

### Step 5: Sample Query
```python
sample_question = "What documents are required for a reimbursement claim, and how does the cashless claim process work?"
sample_result = baseline_answer(sample_question)
print(sample_result["answer"])
```

## Evaluation

### Gold Benchmark Dataset

The system uses a benchmark CSV (`golden_benchmark_dataset.csv`) with 30 examples, each containing:
- `question`: The policy question
- `answer`: The expected ground-truth answer
- `context`: Retrieved context for the answer
- `supporting_sources`: Document references supporting the answer

**Dataset split:**
- Training set: 24 examples (used for GEPA prompt optimization)
- Test set: 6 examples (used for final evaluation)

### Evaluation Metrics (DeepEval)

- **Relevance**: Measures the relationship between the generated answer and the expected answer
- **Faithfulness**: Measures how faithful the generated answer is to the retrieved policy context
- **Completeness**: Measures whether the answer covers important policy details

### Prompt Optimization with GEPA

The system uses `PromptOptimizer` with the `GEPA` algorithm to continuously optimize prompts based on evaluation feedback. The optimization loop:

1. Starts with baseline prompts
2. Evaluates against the test set using GEval metrics
3. Generates varied prompts using genetic operators
4. Selects the best prompts based on TieBreaker criteria
5. Iteratively improves answer quality

## Business Impact

A successful pilot is measured through:

- **Strong benchmark performance** with high relevance, faithfulness, and completeness scores
- **Accurate source-grounded responses** with verifiable document references
- **Reduced dependency on the back-office helpdesk** (target: decrease 11,000-13,000 monthly queries)
- **Improved confidence during customer conversations** for sales representatives
- **Scaling across direct sales force and broker/partner ecosystem**
- **Shortened time-to-productivity for new representatives** (from 9-12 months toward faster onboarding)
- **Reduced misrepresentation risk** through transparent audit trails of retrieved sources

## Running the Notebook

1. Open the notebook: `RAG/Session Notebook Retrieval Augmented Generation.ipynb`
2. Run all cells sequentially from the top
3. Ensure the `config.json` file is present with valid API credentials
4. Verify that all PDF documents and the benchmark CSV are in the `RAG/` directory
5. The notebook will guide you through:
   - Installing dependencies
   - Loading and chunking policy documents
   - Building the Chroma vector store
   - Running the baseline RAG pipeline
   - Evaluating answer quality
   - Optimizing prompts with GEPA