# AI Projects - ShopNest and Agentic AI Intro

This repository contains two AI projects:

## ShopNest Global - AI-Powered Support Ticket Intelligence
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
Build an AI-assisted system that improves the consistency and quality of customer support operations at scale, reducing the 2-3 minute decoding time per ticket.

**Data:**
- 30 support tickets with structured data (support_ticket_id and support_ticket_text)

## AI — Agentic AI Intro
A beginner-friendly intro to Agentic AI: how a language model can use tools, remember preferences, and solve multi-step tasks.

**What you'll see:**
- A simple email-sending assistant built with LangChain and LangGraph
- Web search combined with email in one request (find a restaurant, then send an invite)
- Memory across messages (remembers cuisine and dinner-time preferences)
- A demo of MCP for discovering tools from a remote server
- A math reasoning example with step-by-step working

**How to run:**
1. Create a Python virtual environment and install the packages listed in the first notebook cell.
2. Copy the .env.example file to .env in the AgenticAI folder and add your API key and base URL.
3. Open the notebook in Jupyter and run the cells top to bottom.

**Notes:**
- Real API keys are never committed. Only the .env.example template is shared.
- The notebook handles offline cases gracefully (e.g. MCP server unreachable).
