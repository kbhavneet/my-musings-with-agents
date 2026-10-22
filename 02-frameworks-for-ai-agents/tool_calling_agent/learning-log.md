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

## 2. Errors and Debugging Lessons

| Symptom | Cause | Status |
|---|---|---|
| 401 on `router.huggingface.co` | The `HF_TOKEN` stored in the Colab secret was rejected by Hugging Face | **Confirmed** (the `whoami` error said the token from the Colab vault was invalid). |
| Token lacked "Make calls to Inference Providers" | Seen in the permissions setting | Observed; whether it also contributed to the failure was not isolated |
| 400 `model_not_supported` | No enabled provider serves the requested model | **Hypothesis** (the message says this, but I did not inspect provider settings) |
| Default model differs from the course | smolagents defaults differ by version | **Confirmed** difference; cause (version drift) is a hypothesis |
| 402 Payment Required | Out of Inference Provider credits | **Confirmed** by the error message |
| `No results found!` in every agent step | The search tool failed | **Confirmed** that the tool (and not the model) failed |
| `ddgs` missing | Package not installed | Installed; **did not** fix the DuckDuckGo problem |
| DuckDuckGo returns no results | Blocking or rate limiting from Colab IPs | **Hypothesis** (not tested from another network) |
| Bing returns unrelated VLC pages | Unknown | **Hypothesis** only (locale or endpoint behaviour) |

**Patterns across debugging**

1. **Errors came in layers.** Authentication (401), then model availability (400), then billing (402), then tool failure. Fixing each one revealed the next.
2. **Isolating components was most effective.** `whoami` isolated authentication and calling each search tool alone isolated the search problem. Debugging through the full agent would have hidden both.
3. **The agent's final answer was misleading.** The playlist looked fine but the trace showed no search had succeeded.
4. Falling back to the default model and switching to the Bing engine both failed. Both were reasonable guesses that needed testing and testing them is what ruled them out.