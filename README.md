# AI Projects Repository - Main Branch

This repository contains three AI/ML projects focused on different domains: Retrieval-Augmented Generation (RAG) for health insurance policy intelligence, Agentic AI introductory concepts, and AI-powered support ticket intelligence for e-commerce.

## Projects Overview

### 1. RAG - Retrieval Augmented Generation for Health Insurance Policy Intelligence
**Directory:** `RAG/`

A Retrieval-Augmented Generation system designed for MeridianHealth Insurance, enabling sales representatives to retrieve accurate, source-grounded answers from health insurance documentation in real-time during live customer conversations.

**Key Features:**
- Ingests 4 policy documents (brochure, T&C, claim exclusions, claim guide) into a searchable vector database
- Retrieves relevant policy context when a sales representative asks a question
- Generates precise, grounded answers using a Large Language Model
- Returns supporting source passages and document references alongside every answer
- Handles comparative and cross-document questions (benefits/exclusions, waiting periods, reimbursement vs. cashless guidance)
- Includes prompt optimization and evaluation using DeepEval and GEPA-based prompt tuning
- Evaluates multiple RAG configurations (retrieval depth, generation parameters) for production readiness

**Data Documents:**
- `Policy_Brocher.pdf` - Main product overview in simple language
- `Health_Insurance_Policy_TandC.pdf` - Formal policy wording and legal clauses
- `List_of_Standard_Claim_Exclusions.pdf` - Items/charges not payable under standard claims
- `Health_Insurance_Claim_Guide.pdf` - Reimbursement and cashless claim processes

**Evaluation:**
- Gold benchmark dataset with 30 examples (24 training, 6 test)
- DeepEval metrics: Relevance, Faithfulness, Completeness
- GEPA prompt optimization for continuous improvement

**Business Impact:**
- Reduces dependency on back-office helpdesk (11,000-13,000 monthly queries)
- Improves sales representative confidence during customer conversations
- Shortens time-to-productivity for new representatives (from 9-12 months)
- Reduces misrepresentation risk through transparent audit trails

**Setup:**
- Python 3.14+
- OpenAI API key in `config.json`
- Required: `pip install chromadb==1.5.9 langchain-community==0.4.1 langchain-chroma==1.1.0 langchain-openai==1.2.1 langchain-text-splitters==1.1.2 pandas>=2.2.0 numpy>=1.26.0 scikit-learn>=1.4.0 python-dotenv>=1.0.1 tqdm>=4.66.0 pypdf>=5.0.0 deepeval`

---

### 2. Agentic AI Introduction
**Directory:** `AgenticAI/`

A beginner-friendly introduction to Agentic AI: how a language model can use tools, remember preferences, and solve multi-step tasks.

**Key Features:**
- Simple email-sending assistant built with LangChain and LangGraph
- Web search combined with email in one request (find a restaurant, then send an invite)
- Memory across messages (remembers cuisine and dinner-time preferences)
- Demo of MCP for discovering tools from a remote server
- Math reasoning example with step-by-step working

**Setup:**
- Python virtual environment required
- Copy `.env.example` to `.env` in the AgenticAI folder and add API key and base URL
- Open the notebook in Jupyter and run cells top to bottom

**Notes:**
- Real API keys are never committed. Only the `.env.example` template is shared.
- The notebook handles offline cases gracefully (e.g., MCP server unreachable).

---

### 3. ShopNest Global - AI-Powered Support Ticket Intelligence
**Directory:** `ShopNest_Project/`

A Proof of Concept (POC) for an AI-powered ticket intelligence system designed for ShopNest Global, a large-scale e-commerce platform operating across 30+ countries with over 50 million active customers and 2,000+ support agents.

**Key Features:**
- Summarises incoming raw, unstructured tickets into clean, concise summaries
- Evaluates summary quality using LLM-as-Judge approach
- Generates professional, empathetic customer responses grounded in support policies
- Evaluates generated response quality using LLM-as-Judge
- Compiles all outputs into a structured table for downstream use

**Business Context:**
- ShopNest processes over 200,000 orders daily
- During peak periods, ticket volume can spike from ~5,000 to ~15,000 daily
- Tickets are highly unstructured with background details, abbreviations, and order codes
- Human agents spend 2-3 minutes just decoding each ticket before resolution

**Objective:**
Build an AI-assisted system that improves the consistency and quality of customer support operations at scale, reducing the 2-3 minute decoding time per ticket and enabling agents to handle higher volumes more effectively.

**Data:**
- 30 support tickets with structured data (`support_ticket_id` and `support_ticket_text`)

**Setup:**
- Python 3.14+
- OpenAI API key in `config.json`
- Required: `pip install pandas==2.2.2 langchain-openai==1.1.12 openai==2.31.0`

## Repository Structure

```
AI/                          # Main branch
├── AgenticAI/               # Agentic AI introductory notebook and config
├── RAG/                     # Retrieval Augmented Generation for health insurance
│   ├── Health_Insurance_Claim_Guide.pdf
│   ├── Health_Insurance_Policy_TandC.pdf
│   ├── List_of_Standard_Claim_Exclusions.pdf
│   ├── Policy_Brocher.pdf
│   ├── Session Notebook Retrieval Augmented Generation.ipynb
│   ├── README.md
│   ├── chroma_db/           # ChromaDB vector store
│   └── golden_benchmark_dataset.csv
├── ShopNest_Project/        # AI-powered support ticket intelligence POC
│   ├── Full_code_Support_Ticket_Analysis.ipynb
│   ├── config.json
│   ├── support_ticket_data.csv
│   └── README.md
├── .gitignore
├── README.md                # This consolidated README
└── config.json              # Root config (if needed)
```

## Common Setup

### OpenAI Configuration
All three projects require an OpenAI API key. Create a `config.json` file with:

```json
{
  "OPENAI_API_KEY": "your-api-key",
  "OPENAI_API_BASE": "https://api.openai.com/v1"
}
```

### Python Dependencies
Each project has specific dependencies, but common packages include:
- `pandas` - data manipulation
- `langchain*` - LLM framework integration
- `openai` - OpenAI API client
- `chromadb` / `pinecone` - vector storage (RAG project)
- `deepeval` - evaluation and prompt optimization (RAG project)

## How to Run

1. **Clone the repository:**
   ```bash
   git clone https://github.com/kavitha-sugatha/ai.git
   cd ai
   ```

2. **Create a virtual environment:**
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # On Windows: .venv\Scripts\activate
   ```

3. **Install dependencies** for the project you want to run:
   - RAG: `pip install -r requirements.txt` (or see individual project READMEs)
   - Agentic AI: Install packages from the notebook's first cell
   - ShopNest: `pip install pandas==2.2.2 langchain-openai==1.1.12 openai==2.31.0`

4. **Configure API keys:**
   - Copy `.env.example` to `.env` if present
   - Add your OpenAI API key to `config.json`

5. **Run the notebooks:**
   - Open the desired `.ipynb` file in Jupyter
   - Run all cells sequentially from the top

## Branch Information

- **`main`**: Contains all three projects (AgenticAI, RAG, ShopNest_Project)
- **`feat/agentic_rag`**: RAG retrieval augmented generation materials (merged into main)
- **`feat/prompting`**: ShopNest Project AI-powered support ticket intelligence (merged into main)
- **`feat/agentic_ai_intro`**: Agentic AI introductory notebook (merged into main)

## License

This repository is for educational and proof-of-concept purposes. Real API keys should never be committed - only `.env.example` templates are shared.