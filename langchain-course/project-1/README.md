# Project 1 — LangChain Hello World

A minimal LangChain application that demonstrates the core building blocks of an LLM chain: prompt templates, an LLM, and the pipe (`|`) composition pattern.

## What It Does

Given a block of biographical text about a person, the chain asks the LLM to produce:
1. A short summary
2. Two interesting facts

The example input is a Wikipedia-style biography of Elon Musk.

## Stack

| Component | Library |
|-----------|---------|
| LLM | `langchain-openai` (`gpt-5`) |
| Prompt | `langchain_core.prompts.PromptTemplate` |
| Env vars | `python-dotenv` |
| Package manager | `uv` |

## Project Structure

```
project-1/
├── main.py          # Chain definition and entry point
├── pyproject.toml   # Dependencies
└── README.md
```

## Getting Started

1. **Install dependencies**
   ```bash
   uv sync
   ```

2. **Set your OpenAI API key** — create a `.env` file:
   ```
   OPENAI_API_KEY=sk-...
   ```

3. **Run**
   ```bash
   uv run python main.py
   ```

## Key Concepts Demonstrated

- `PromptTemplate` — parameterised prompt with `input_variables`
- LCEL pipe syntax — `prompt | llm` builds a runnable chain
- `chain.invoke({"information": ...})` — executes the chain synchronously
