# Gemma Book Narrator Agent Service

This is the local Agent service for **Gemma Book Narrator Agent**, a multilingual PDF-to-narration and audio learning assistant powered by **Gemma 4 26B**.

The Agent service receives narration requests from the backend, reads selected PDF pages, builds prompts, calls the local Gemma 4 26B model, and returns generated narration scripts to the backend.

---

## Role in the System

The Agent service is the core reasoning and generation layer of the project.

```text
Vue Frontend
    |
    | HTTP API
    v
FastAPI Backend
    |
    | Send PDF path and task settings
    v
Local Agent Service
    |
    | Read selected PDF pages
    | Build narration prompt
    | Call local Gemma 4 26B model
    v
Generated narration script
    |
    v
FastAPI Backend
    |
    | Save script and generate audio
    v
Vue Frontend
```

The frontend does not call Gemma 4 directly.
The backend sends the user request to this Agent service.
The Agent then calls the local Gemma 4 26B model.

---

## Main Responsibilities

* Receive Agent run requests from the backend.
* Parse user narration requests.
* Read selected PDF pages.
* Extract source text from PDF files.
* Build prompts for Gemma 4 26B.
* Apply reading mode requirements.
* Apply narration style requirements.
* Apply user extra task requirements.
* Apply output language requirements.
* Call the local Gemma 4 26B model.
* Return the final narration script to the backend.
* Save debug prompts for troubleshooting.

---

## Technology Stack

| Component        | Technology                                |
| ---------------- | ----------------------------------------- |
| API Service      | FastAPI / Uvicorn                         |
| Agent Logic      | Python                                    |
| Model Runtime    | Ollama or Ollama-compatible local runtime |
| Model            | Gemma 4 26B                               |
| PDF Reading      | Python PDF reader                         |
| Prompt Debugging | Saved prompt files                        |

---

## Recommended Local Directory Layout

For the local demo, the frontend, backend, Agent service, and shared PDF directory should be placed on the same machine.

```text
~/Desktop/
├── book-commentary-frontend-rebuild/
│   └── Vue frontend application
├── backend-feature2.1_副本/
│   └── FastAPI backend service
├── agent-master/
│   └── Local Gemma 4 Agent service
└── shared/
    └── books/
        └── uploaded PDF files
```

This local layout is important because the backend sends PDF paths to the Agent service.

Example PDF path:

```text
~/Desktop/shared/books/example.pdf
```

Since the backend and Agent run locally on the same machine, the Agent can directly read the uploaded PDF files from this shared directory.

---

## Project Structure

```text
agent-master/
├── src/
│   ├── api/
│   │   └── server.py                  # Agent API service
│   ├── agents/
│   │   └── langchain_agent.py         # Agent orchestration logic
│   ├── prompts/
│   │   └── agent_prompts.py           # Agent system prompts
│   ├── tools/
│   │   ├── langchain_tools.py         # Tool definitions used by Agent
│   │   └── narrator.py                # Narration generation logic
│   └── ...
├── debug_prompts/
│   └── saved prompts for debugging
├── requirements.txt
└── README.md
```

---

## Local Gemma 4 26B Model Deployment

This project uses:

```text
gemma4:26b
```

as the intended main local generation model.

The Agent service calls Gemma 4 26B through a local model runtime such as **Ollama** or an Ollama-compatible local API service.

Gemma 4 26B is responsible for generating the final narration script from extracted PDF content and user requirements.

---

## Hardware Requirements for Gemma 4 26B

Gemma 4 26B is a large model. Local performance depends on quantization, runtime, available memory, context length, and hardware acceleration.

Practical recommendations:

| Setup                                      | Suggested Hardware                  |
| ------------------------------------------ | ----------------------------------- |
| Minimum local test with quantized model    | 24 GB RAM or more                   |
| Recommended local use                      | 32 GB RAM or more                   |
| Better experience for longer PDF narration | 32–64 GB system memory              |
| GPU acceleration                           | High-VRAM GPU recommended           |
| Long-context or detailed generation        | More memory is strongly recommended |

If the local machine cannot run `gemma4:26b` smoothly, a smaller Gemma 4 model can be used for testing. However, the intended model for this project is:

```text
gemma4:26b
```

---

## Install Ollama

This demo assumes that the model is available through Ollama or an Ollama-compatible runtime.

Install Ollama from:

```text
https://ollama.com
```

After installation, check whether Ollama is available:

```bash
ollama --version
```

---

## Pull Gemma 4 26B

Pull the model:

```bash
ollama pull gemma4:26b
```

Check available local models:

```bash
ollama list
```

You should see:

```text
gemma4:26b
```

---

## Test Gemma 4 26B Locally

Run a simple local model test:

```bash
ollama run gemma4:26b
```

Example test prompt:

```text
Summarize the following paragraph in English.
```

If the model responds correctly, the local model runtime is working.

---

## Local Model Runtime Configuration

The Agent should be configured to call the local Gemma 4 26B model.

Example configuration for Ollama:

```env
MODEL_NAME=gemma4:26b
OLLAMA_BASE_URL=http://127.0.0.1:11434
```

If the Agent uses an OpenAI-compatible local gateway, use a compatible endpoint:

```env
OPENAI_BASE_URL=http://127.0.0.1:11434/v1
OPENAI_MODEL=gemma4:26b
```

The exact environment variable names may depend on the Agent implementation, but the core requirement is that the Agent service calls the local `gemma4:26b` model.

---

## Start the Local Model Runtime

Start Ollama:

```bash
ollama serve
```

If Ollama is already running in the background, this step may not be necessary.

Check that the model is available:

```bash
ollama list
```

If `gemma4:26b` is not listed, pull it:

```bash
ollama pull gemma4:26b
```

---

## Start the Agent Service

Open a new terminal and run:

```bash
cd ~/Desktop/agent-master

uvicorn src.api.server:app --reload --host 127.0.0.1 --port 8000
```

Agent service URL:

```text
http://127.0.0.1:8000
```

Health check:

```bash
curl http://127.0.0.1:8000/api/v1/health
```

If the health check returns successfully, the Agent service is running.

---

## Backend Connection

The FastAPI backend connects to this local Agent service through:

```env
AGENT_BASE_URL=http://127.0.0.1:8000
```

If the backend reports:

```text
Connection refused: localhost:8000
```

it usually means the Agent service is not running or the backend is pointing to the wrong Agent URL.

---

## API Used by Backend

The backend calls the Agent service through:

```text
POST /api/v1/agent/run
```

Example request body:

```json
{
  "user_request": "Read the PDF path and generate a narration script...",
  "reset_context": true
}
```

The Agent response contains the generated narration result and task metadata.

---

## PDF Path Requirement

The Agent must be able to access the PDF path sent by the backend.

In this local demo, uploaded PDFs are stored in:

```text
~/Desktop/shared/books
```

Example:

```text
/Users/your-name/Desktop/shared/books/example.pdf
```

Because the Agent and backend run on the same machine, the Agent can directly read this path.

If the Agent runs on a remote server, it cannot read local macOS paths. In that case, uploaded PDFs must be synchronized to the remote server. For this project demo, the recommended setup is to run the Agent locally.

---

## Why the Agent Runs Locally

In earlier remote-Agent setups, the backend could save a PDF to a local path such as:

```text
/Users/your-name/Desktop/shared/books/example.pdf
```

A remote cloud Agent cannot read this local path.

To avoid this file path problem, the final demo runs the Agent locally on the same machine as the backend.

This makes the workflow simpler and more reliable:

```text
Frontend -> Local Backend -> Local Agent -> Local shared/books -> Local Gemma 4 26B
```

---

## Reading Modes

The Agent supports different reading modes passed by the backend.

### Quick Reading

Quick Reading is used for concise understanding.

It focuses on:

* main characters,
* key events,
* important turning points,
* main conflict,
* visible outcomes.

It compresses secondary descriptions and avoids overly long analysis.

### Deep Reading

Deep Reading is used for richer interpretation.

It keeps more details and can support:

* more complete event development,
* richer character relationships,
* writing style analysis,
* character analysis,
* deeper learning-oriented explanation.

---

## Narration Styles

The Agent can receive different narration styles from the backend, such as:

* Short-video style
* Bedtime story style
* Film commentary style

The style affects the tone, pacing, and narrative form of the generated script.

---

## Output Language Support

The Agent should follow the output language requirement passed by the backend.

Supported output languages:

* English
* Chinese
* French
* Spanish
* Russian
* Arabic

The generated script should match the selected output language because the backend audio service uses the final script directly for text-to-speech.

---

## Output Language and Voice Mapping

| Output Language | Suggested Voices                 |
| --------------- | -------------------------------- |
| English         | Ava, Jenny, Guy, Brian           |
| Chinese         | Xiaoxiao, Yunxi, Yunyang, Xiaoyi |
| French          | Denise, Henri                    |
| Spanish         | Elvira, Alvaro                   |
| Russian         | Svetlana, Dmitry                 |
| Arabic          | Zariyah, Hamed                   |

If the generated script language does not match the selected voice language, audio generation may fail or sound unnatural.

---

## Debugging Prompts

The Agent may save final prompts into:

```text
debug_prompts/
```

To inspect the latest prompt:

```bash
cd ~/Desktop/agent-master

latest=$(ls -t debug_prompts/*.txt | head -1)

echo "$latest"

cat "$latest"
```

Check whether the final prompt contains the expected output language requirement.

Example:

```text
The final narration script MUST be written entirely in English.
Chinese output is forbidden.
```

If the final prompt does not contain the selected output language requirement, the model may return text in the wrong language.

---

## Common Problems

### 1. Agent Service Is Not Running

Backend error:

```text
Connection refused: localhost:8000
```

Solution:

```bash
cd ~/Desktop/agent-master

uvicorn src.api.server:app --reload --host 127.0.0.1 --port 8000
```

Then test:

```bash
curl http://127.0.0.1:8000/api/v1/health
```

---

### 2. Gemma 4 26B Is Not Available

Error symptoms:

* Agent starts but generation fails.
* The local model runtime cannot find `gemma4:26b`.
* Model calls fail.

Check available models:

```bash
ollama list
```

If the model is missing:

```bash
ollama pull gemma4:26b
```

Test the model:

```bash
ollama run gemma4:26b
```

---

### 3. PDF File Does Not Exist

Cause:

The backend sent a PDF path that the Agent cannot access.

Solution:

Make sure the PDF exists in:

```text
~/Desktop/shared/books
```

Check:

```bash
ls -lh ~/Desktop/shared/books
```

If the Agent and backend are not on the same machine, the path may not be accessible. For this project, run both services locally.

---

### 4. Output Language Is Wrong

Check the latest debug prompt:

```bash
cd ~/Desktop/agent-master

latest=$(ls -t debug_prompts/*.txt | head -1)

cat "$latest"
```

Confirm that the final prompt includes the selected output language requirement.

For example:

```text
The final narration script MUST be written entirely in Russian.
Chinese output is forbidden.
```

If the language requirement is missing, inspect the backend request and Agent prompt-building logic.

---

### 5. Syntax Error After Editing Prompts

If Python reports a syntax error after editing prompt text, make sure the prompt is inside a Python string.

Correct:

```python
prompt = """
Your prompt text here.
"""
```

Wrong:

```python
Your prompt text here.
```

---

## Local Demo Workflow

Recommended startup order:

1. Start Ollama or the local model runtime.
2. Make sure `gemma4:26b` is available.
3. Start the Agent service.
4. Start the backend service.
5. Start the frontend service.
6. Upload or select a PDF.
7. Generate narration.
8. Review script and audio.

---

## Security Notes

Do not commit real passwords, API keys, server credentials, or `.env` files.

Recommended `.gitignore` entries:

```gitignore
.env
__pycache__/
*.pyc
.DS_Store
debug_prompts/
```

---

## Hackathon Relevance

This Agent service is part of the **Gemma Book Narrator Agent** project for the Gemma 4 Good Hackathon.

It demonstrates how a local Gemma 4 26B model can be used to transform PDF documents into accessible, multilingual, audio-friendly learning content.

The project focuses on:

* Future of Education
* Digital Equity and Inclusivity
* Multilingual learning support
* Accessible PDF understanding
* Audio-based learning

---

## License

This Agent service is for educational and hackathon demonstration purposes.
