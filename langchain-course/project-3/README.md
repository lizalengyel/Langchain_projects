# Project 3 — ReAct Agent Loop with Tool Calling

Implements a **ReAct (Reasoning + Acting) agent loop** using LangChain. The agent acts as a shopping assistant that can look up product prices and apply discounts by calling tools, looping until it has enough information to answer.

Two implementations are provided side-by-side to illustrate the difference between raw function calling and LangChain's higher-level tool abstraction.

---

## Files

| File | Description |
|------|-------------|
| `1_agent_loop_langchain_tool_calling.py` | Agent using LangChain's `@tool` decorator — schema is auto-generated from type hints and docstrings |
| `1_agent_loop_raw_function_calling.py` | Agent using raw `@traceable(run_type="tool")` — manual tool definition without the `@tool` decorator |

---

## How the Agent Loop Works

The agent follows the **ReAct pattern** — Reasoning and Acting in a cycle:

```
REASON → ACT → OBSERVE → (repeat) → Final Answer
```

1. **REASON** — The LLM receives the conversation history and decides which tool to call (or returns a final answer if no tool is needed)
2. **ACT** — The agent executes the selected tool with the LLM-chosen arguments
3. **OBSERVE** — The tool result is appended to the message history as a `ToolMessage`
4. **REPEAT** — The loop runs again with the updated history until the LLM produces a response with no tool calls

The loop is capped at `MAX_ITERATIONS = 10` to prevent infinite loops.

Only one tool is executed per iteration (the first tool call returned by the LLM), enforcing a strict sequential reasoning chain.

---

## Tools

### `get_product_price(product: str) -> float`
Looks up a product's price from a hardcoded catalog.

| Product | Price |
|---------|-------|
| laptop | $1,299.99 |
| headphones | $149.95 |
| keyboard | $89.50 |

### `apply_discount(price: float, discount_tier: str) -> float`
Applies a percentage discount and returns the final price.

| Tier | Discount |
|------|----------|
| bronze | 5% |
| silver | 12% |
| gold | 23% |

---

## Example Run

```
Question: What is the price of a laptop after applying a gold discount?
============================================================

--- Iteration 1 ---
  [Tool Selected] get_product_price with args: {'product': 'laptop'}
    >> Executing get_product_price(product='laptop')
  [Tool Result] 1299.99

--- Iteration 2 ---
  [Tool Selected] apply_discount with args: {'price': 1299.99, 'discount_tier': 'gold'}
    >> Executing apply_discount(price=1299.99, discount_tier='gold')
  [Tool Result] 1000.99

Final Answer: The price of a laptop after applying a gold (23%) discount is $1,000.99.
```

---

## Key Difference Between the Two Implementations

### LangChain `@tool` decorator (`1_agent_loop_langchain_tool_calling.py`)
```python
@tool
def get_product_price(product: str) -> float:
    """Look up the price of a product in the catalog."""
    ...
```
LangChain automatically generates a JSON schema for the tool from the function's name, type hints, and docstring. The LLM receives this schema via `llm.bind_tools(tools)`.

### Raw function calling (`1_agent_loop_raw_function_calling.py`)
```python
@traceable(run_type="tool")
def get_product_price(product: str) -> float:
    """Look up the price of a product in the catalog."""
    ...
```
Tools are plain Python functions decorated only with LangSmith's `@traceable` for observability. This shows what happens under the hood before LangChain's abstractions are applied.

---

## Model Providers

The agent is provider-agnostic via `init_chat_model`. Switch providers by uncommenting the relevant lines:

```python
# OpenAI
MODEL = "gpt-4o"
MODEL_PROVIDER = "openai"

# Anthropic Claude
MODEL = "claude-3-5-sonnet-20241022"
MODEL_PROVIDER = "anthropic"

# Google Gemini
MODEL = "gemini-1.5-pro"
MODEL_PROVIDER = "google-vertexai"

# Groq
MODEL = "mixtral-8x7b-32768"
MODEL_PROVIDER = "groq"

# Azure OpenAI
MODEL = "gpt-4"
MODEL_PROVIDER = "azure-openai"
```

---

## Setup

**Requirements:** Python 3.13+, [uv](https://github.com/astral-sh/uv)

```bash
# Install dependencies
uv sync

# Set environment variables
cp .env.example .env
# Add your API key (e.g. OPENAI_API_KEY, ANTHROPIC_API_KEY)

# Run LangChain tool calling version
uv run python 1_agent_loop_langchain_tool_calling.py

# Run raw function calling version
uv run python 1_agent_loop_raw_function_calling.py
```

### Environment Variables

| Variable | Required for |
|----------|-------------|
| `OPENAI_API_KEY` | OpenAI provider |
| `ANTHROPIC_API_KEY` | Anthropic provider |
| `LANGSMITH_API_KEY` | LangSmith tracing (optional) |

---

## Dependencies

```toml
langchain
langchain-openai
langchain-anthropic
langchain-ollama
python-dotenv
langsmith
```
