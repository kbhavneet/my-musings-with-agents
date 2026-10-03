```md
# Unit 2 — CodeAgent & Web Search Experiment

This notebook is part of the Hugging Face Agents Course (Unit 2). I used Google Colab to experiment with `smolagents`, `CodeAgent`, `InferenceClientModel` and `WebSearchTool`.

## What I worked on

- Set up and authenticated Hugging Face in Google Colab.
- Verified `smolagents` version `1.26.0`.
- Created a `CodeAgent` using:
  - `Qwen/Qwen3-Next-80B-A3B-Instruct`
  - Novita as the inference provider
  - `WebSearchTool` for external search
- Initially tested the default model/provider configuration and encountered a `model_not_supported` error.
- Explicitly configured the Novita provider, but the first model variant returned a `404` error.
- Switched to the Instruct version of Qwen, which successfully connected to Novita.

## Debugging WebSearchTool

The default DuckDuckGo search initially returned:

```text
No results found! Try a less restrictive/shorter query.
```

I tested the search tool independently and inspected its implementation. The DuckDuckGo request itself was returning HTML, but the parser was looking for HTML elements that were no longer present in the response.

I then tested the alternative Bing backend:

```python
WebSearchTool(engine="bing")
```

Bing successfully returned relevant search results, so I used it for the agent.

## Successful agent run

With the following configuration:

```python
CodeAgent(
    tools=[WebSearchTool(engine="bing")],
    model=InferenceClientModel(
        model_id="Qwen/Qwen3-Next-80B-A3B-Instruct",
        provider="novita"
    )
)
```

the agent successfully generated code, used the web-search tool, processed the results and produced a final response.

This confirmed the complete workflow:

```text
Prompt
  ↓
CodeAgent
  ↓
Qwen model
  ↓
Generated code
  ↓
Bing WebSearchTool
  ↓
Search results
  ↓
Final answer
```

## Further issue: inference timeout

Subsequent runs started returning:

```text
504 Gateway Time-out
```

after approximately 120 seconds. I tried running the agent with streaming enabled. This allowed the run to proceed further, but eventually the inference provider returned:

```text
402 Payment Required
```

The response indicated that the monthly included Hugging Face Inference Provider credits had been exhausted.

### Current status

The code and configuration were successfully tested earlier, but further runs are currently blocked by the exhausted monthly inference credits.

