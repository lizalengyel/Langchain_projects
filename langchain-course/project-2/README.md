# Project 2: LangChain Agent with Web Search

## Overview
This project demonstrates how to build an intelligent agent using **LangChain** that can search the web and provide structured responses with sources. The agent uses OpenAI's language model combined with Tavily Search API to find real-time information and return answers in a structured format.

## Features
- **AI Agent**: Uses LangChain's agent framework with GPT-5 as the language model
- **Web Search Integration**: Integrates with Tavily Search for real-time web searches
- **Structured Responses**: Returns answers with cited sources using Pydantic models
- **Tool Usage**: The agent can utilize search tools to answer user queries

## Project Structure
```
project-2/
├── main.py          # Main application file
├── .env             # Environment variables (API keys)
├── requirements.txt # Python dependencies
└── README.md        # This file
```

## Technologies Used
- **LangChain**: Framework for building applications with language models
- **OpenAI API**: GPT-5 language model
- **Tavily Search**: Web search API integration
- **Pydantic**: Data validation and modeling
- **Python 3.13+**

## Installation

### 1. Clone and Setup
```bash
cd project-2
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. Configure Environment Variables
Create a `.env` file in the project root and add your API keys:
```
OPENAI_API_KEY=your_openai_api_key
TAVILY_API_KEY=your_tavily_api_key
```

## Usage

Run the main script:
```bash
python main.py
```

### Example Query
The default example searches for 3 job positions using LangChain in Budapest on LinkedIn:
```python
agent.invoke({
    "messages": HumanMessage(
        content="Search for 3 job positions using langchain in Budapest on Linkedin and list their details"
    )
})
```

## Response Format
The agent returns responses in the following Pydantic model structure:

```python
class Source(BaseModel):
    url: str  # URL of the source

class AgentResponse(BaseModel):
    answer: str  # The agent's answer to the query
    sources: List[Source]  # List of sources used
```

## How It Works
1. The user provides a query as a `HumanMessage`
2. The agent processes the query and decides whether to use the Tavily Search tool
3. The search tool retrieves relevant web results
4. The LLM synthesizes the results into a structured response
5. The response includes both the answer and the sources used

## Customization

### Change the Query
Modify the `HumanMessage` content in the `main()` function to ask different questions.

### Use Different Model
Update the model in `main.py`:
```python
llm = ChatOpenAI(model='gpt-4')  # or another available model
```

### Add More Tools
Extend the `tools` list with additional LangChain tools for different capabilities.

## Notes
- Ensure your OpenAI API key has access to GPT-5
- Tavily API provides real-time web search capabilities
- The agent autonomously decides when and how to use available tools
