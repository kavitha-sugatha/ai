# AI — Agentic AI Intro

A beginner-friendly intro to Agentic AI: how a language model can use tools,
remember preferences, and solve multi-step tasks.

Start with the notebook in the AgenticAI folder.

## What you'll see

- A simple email-sending assistant built with LangChain and LangGraph
- Web search combined with email in one request (find a restaurant, then send an invite)
- Memory across messages (remembers cuisine and dinner-time preferences)
- A demo of MCP for discovering tools from a remote server
- A math reasoning example with step-by-step working

## How to run

1. Create a Python virtual environment and install the packages listed in the first notebook cell.
2. Copy the .env.example file to .env in the AgenticAI folder and add your API key and base URL.
3. Open the notebook in Jupyter and run the cells top to bottom.

## Notes

- Real API keys are never committed. Only the .env.example template is shared.
- The notebook handles offline cases gracefully (e.g. MCP server unreachable).
