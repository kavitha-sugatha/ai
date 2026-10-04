# ShopNest Global - AI-Powered Support Ticket Intelligence

## Overview

This project is a Proof of Concept (POC) for an AI-powered ticket intelligence system designed for ShopNest Global, a large-scale e-commerce platform operating across 30+ countries with over 50 million active customers and 2,000+ support agents.

## Business Context

ShopNest processes over 200,000 orders daily, generating significant support ticket volume. During peak periods, ticket volume can spike from ~5,000 to ~15,000 daily. Key challenges include:

- Tickets are highly unstructured with background details, abbreviations, and order codes
- Human agents spend 2-3 minutes just decoding each ticket before resolution
- High volume contributes to agent fatigue and higher error rates
- Manual response drafting is slow and inconsistent

## Objective

Build an AI-assisted system that:

1. **Summarises** incoming raw, unstructured tickets into clean, concise summaries
2. **Evaluates** summary quality using LLM-as-Judge approach
3. **Generates** professional, empathetic customer responses grounded in support policies
4. **Evaluates** generated response quality using LLM-as-Judge
5. **Compiles** all outputs into a structured table for downstream use

## Data

- **30 support tickets** with structured data:
  - `support_ticket_id`: Unique identifier (Integer)
  - `support_ticket_text`: Free-form text describing the issue (String)

## Setup

### Prerequisites

- Python 3.14+
- OpenAI API key

### Installation

```bash
# Install required libraries
pip install pandas==2.2.2 langchain-openai==1.1.12 openai==2.31.0
```

### Configuration

1. Create a `config.json` file with your OpenAI credentials:
```json
{
  "OPENAI_API_KEY": "your-api-key",
  "OPENAI_API_BASE": "https://api.openai.com/v1"
}
```

## How It Works

1. **Data Loading**: Load support tickets from CSV file
2. **Summarization**: Use LLM (gpt-4o-mini) to summarize each ticket
3. **Evaluation**: Score summaries and responses using LLM-as-Judge
4. **Response Generation**: Create professional, empathetic replies
5. **Compilation**: Combine all outputs into a single structured table

## Key Components

- **Summarization System**: System prompt guides the LLM to identify main issues, ignore unnecessary details, and keep summaries concise
- **Response Generation**: Creates professional responses based on support policies
- **LLM Evaluation**: Automatic scoring of both summaries and responses

## Output

The system exports a consolidated table containing:
- Original ticket text
- Generated summaries
- Evaluation scores for summaries
- Generated responses
- Evaluation scores for responses

## Business Impact

This POC demonstrates that AI-assisted summarization and response generation can significantly improve the consistency and quality of customer support operations at scale, reducing the 2-3 minute decoding time per ticket and enabling agents to handle higher volumes more effectively.