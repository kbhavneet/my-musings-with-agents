# First Agent

This project is part of the [Hugging Face Agents Course](https://huggingface.co/learn/agents-course/).

It contains my implementation and experiments while learning how to build and run an agent locally with `smolagents`.

## Setup & Running Locally

### Environment

- Created a Python virtual environment
- Installed the project dependencies
- Configured Hugging Face authentication using `HF_TOKEN`

### Running the Agent

The agent can be started locally with:

```bash
python app.py

## Testing the Tools

### Timezone Tool

Tested the `get_current_time_in_timezone` tool locally to verify that the agent can retrieve the current time for a requested timezone.

### Image Generation

Tested the image-generation tool locally and confirmed that the agent can generate an image.

The initial test exposed a separate issue: the generated image was returned by the agent but was not being rendered correctly in the Gradio UI.

## Gradio UI Compatibility Fix

The initial local agent could generate an `AgentImage`, but the image was not displayed correctly in the Gradio interface.

### Investigation

- Inspected the streamed agent output.
- Found that the final result was wrapped in a `FinalAnswerStep`.
- Inspected the installed `smolagents==1.13.0` implementation.
- Compared its `gradio_ui.py` with the project's existing `Gradio_UI.py`.
- Identified that the project's UI implementation was incompatible with the installed `smolagents` version.

### Fix

Replaced the project's `Gradio_UI.py` with the compatible implementation from `smolagents==1.13.0`.

The updated UI handles the streamed step types including:

- `PlanningStep`
- `ActionStep`
- `FinalAnswerStep`

For `FinalAnswerStep`, the UI extracts the actual `final_answer` before processing it.

### Verification

Tested image generation again after the change.

The generated image was successfully rendered in the Gradio interface.