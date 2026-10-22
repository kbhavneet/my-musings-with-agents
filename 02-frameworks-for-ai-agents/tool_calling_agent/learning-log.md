# Learning Log: 

**Course notebook:** [Unit 2 /smolagents / tool_calling_agents.ipynb](https://huggingface.co/agents-course/notebooks/blob/main/unit2/smolagents/tool_calling_agents.ipynb)

---

## 1. Overview

The course notebook is short: it builds a `ToolCallingAgent` with a web search tool and asks it to find party music for "the Wayne's mansion". The point of the exercise is to see that a `ToolCallingAgent` emits structured tool calls (`Calling tool: 'web_search' ...`) instead of generating code to execute (`Executing parsed code:`) as a `CodeAgent` does.

The course code did not run as written in my environment. Getting it to work turned into a debugging exercise across five layers:

1. **Authentication** with the Hugging Face Inference API (`HF_TOKEN`)
2. **Model access** through Hugging Face inference providers
3. **Billing and credits** on the free tier
4. **Model provider choice** (switching to `OpenAIServerModel`)
5. **Search tool reliability** (`WebSearchTool` with DuckDuckGo and Bing versus `GoogleSearchTool` with Serper)

**Skills exercised:** reading HTTP error codes (401, 400, 402), isolating a failing component from the agent that wraps it, comparing search backends and reading agent traces.

---