# Gemma Book Narrator Agent

A multilingual PDF-to-narration and audio learning assistant powered by **Gemma 4 26B**.

Gemma Book Narrator Agent transforms long PDF books, textbooks, novels, and reading materials into structured narration scripts and playable audio. It is designed for students, readers, and multilingual learners who need a faster and more accessible way to understand long documents.

---

## Demo Format

This project is submitted as a **recorded functional demo with runnable local project files**.

Because the system consists of multiple coordinated local services — the frontend, backend, local Agent service, and local Gemma 4 model runtime — the live demo is provided through:

1. A public demo video.
2. A public GitHub repository.
3. Local running instructions.
4. Project files and README documentation.

In this demo, all core services run locally on the same machine:

```text
Vue Frontend  ->  FastAPI Backend  ->  Local Agent Service  ->  Local Gemma 4 26B Model
```

Uploaded PDFs are saved into a shared local directory:

```text
~/Desktop/shared/books
```

This allows both the backend and the local Agent service to access the same PDF files directly.

---

## Problem

Many learners struggle with long PDF materials.

A student may receive a long textbook.
A reader may want to understand a novel more efficiently.
A multilingual learner may face extra barriers because the material is not written in their first language.

The knowledge is available, but understanding it still takes time, energy, and language ability.

Long documents are often difficult to read continuously, difficult to summarize accurately, and difficult to reuse for listening or revision.

---

## Solution

Gemma Book Narrator Agent helps users turn static PDF documents into accessible learning content.

Users can:

* upload or select a PDF,
* choose page ranges,
* select reading modes,
* choose narration styles,
* specify output language,
* generate narration scripts,
* listen to generated audio,
* save results in history,
* mark useful tasks as favorites.

The system uses **Gemma 4 26B** as the core local generation model. It reads extracted PDF content and generates structured narration based on the selected reading mode, style, user requirements, and output language.

---

## Key Features

* PDF upload and management.
* Automatic shared book storage.
* Page-range narration.
* Quick Reading and Deep Reading modes.
* Multiple narration styles:

  * Short-video style
  * Bedtime story style
  * Film commentary style
* Multilingual output:

  * English
  * Chinese
  * French
  * Spanish
  * Russian
  * Arabic
* Matching voice selection for audio generation.
* Text-to-speech audio generation.
* Task history.
* Favorites.
* Reusable task parameters.
* Local runnable demo.
* Local Gemma 4 26B deployment through Ollama or an Ollama-compatible runtime.

---

## System Architecture

```text
Vue Frontend
    |
    | HTTP API
    v
FastAPI Backend
    |
    | Save uploaded PDFs
    v
~/Desktop/shared/books
    |
    | Send PDF path and task settings
    v
Local Agent Service
    |
    | Read selected PDF pages
    | Build narration prompt
    v
Local Gemma 4 26B Model
    |
    | Generate narration script
    v
Local Agent Service
    |
    | Return generated script
    v
FastAPI Backend
    |
    | Save result
    | Generate audio
    v
Vue Frontend
    |
    | Display script and audio
    v
User
```

---

## Technology Stack

| Part              | Technology                         |
| ----------------- | ---------------------------------- |
| Frontend          | Vue + Vite                         |
| Backend           | FastAPI                            |
| Database          | SQLite + SQLAlchemy                |
| Agent Service     | Python Agent Service               |
| Model Runtime     | Ollama or compatible local runtime |
| Model             | Gemma 4 26B                        |
| PDF Processing    | Agent PDF Reader                   |
| Audio Generation  | Edge TTS                           |
| API Communication | Axios + Requests                   |

---

## Project Structure

The final GitHub repository uses the following folder names:

```text
book-narrator/
├── README.md
├── frontend/
│   └── Vue frontend application
├── backend/
│   └── FastAPI backend service
├── agent/
│   └── Local Gemma 4 Agent service
└── shared/
    └── books/
        └── uploaded PDF files
```

Each service has its own README:

```text
frontend/README.md
backend/README.md
agent/README.md
```

---

## Recommended Local Directory Layout

For the local demo, place the project under the Desktop directory:

```text
~/Desktop/
└── book-narrator/
    ├── README.md
    ├── frontend/
    ├── backend/
    ├── agent/
    └── shared/
        └── books/
```

The `shared/books` folder is used to store uploaded PDF files.

Because the backend and Agent both run locally, the Agent can directly read the PDF path sent by the backend.

---

## How Gemma 4 26B Is Used

Gemma 4 26B is used as the core local generation model inside the Agent service.

The Agent receives:

* extracted PDF text,
* selected page range,
* reading mode,
* narration style,
* user extra requirements,
* output language requirement.

Gemma 4 26B then generates a structured narration script suitable for audio playback.

The backend receives the final script, saves the result, and generates an audio file.

---

## Local Gemma 4 26B Model Deployment

This project uses:

```text
gemma4:26b
```

as the intended main generation model.

The local Agent service calls the local Gemma 4 26B model through a local model runtime such as **Ollama** or an Ollama-compatible API service.

---

## Hardware Requirements for Gemma 4 26B

Gemma 4 26B is a large model. Local performance depends on quantization, runtime, hardware, context length, and available memory.

Recommended practical hardware:

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

This demo assumes that Gemma 4 26B is available through Ollama or an Ollama-compatible local runtime.

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

Pull the local model:

```bash
ollama pull gemma4:26b
```

Check available models:

```bash
ollama list
```

You should see:

```text
gemma4:26b
```

---

## Test Gemma 4 26B Locally

Run a quick test:

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

The Agent service should be configured to call the local Gemma 4 26B model.

Example configuration for Ollama:

```env
MODEL_NAME=gemma4:26b
OLLAMA_BASE_URL=http://127.0.0.1:11434
```

If the Agent uses an OpenAI-compatible local gateway, use a compatible base URL and model name:

```env
OPENAI_BASE_URL=http://127.0.0.1:11434/v1
OPENAI_MODEL=gemma4:26b
```

The exact environment variable names may depend on the Agent implementation, but the key requirement is that the Agent calls the local Gemma 4 26B model.

---

## Reading Modes

### Quick Reading

Quick Reading is used for concise understanding.

It focuses on:

* main characters,
* key events,
* important turning points,
* main conflict,
* visible outcomes.

It compresses secondary descriptions and avoids long analysis.

### Deep Reading

Deep Reading is used for richer interpretation.

It keeps more details and can support:

* more complete event development,
* richer character relationships,
* writing style analysis,
* character analysis,
* deeper learning-oriented explanation.

---

## Output Languages

The system supports the following output languages:

* English
* Chinese
* French
* Spanish
* Russian
* Arabic

The output language should match the selected voice so that the audio can be generated correctly.

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

## Local Running Guide

Start the system in this order:

1. Start the local Gemma 4 26B model runtime.
2. Start the local Agent service.
3. Start the backend service.
4. Start the frontend service.

---

## 1. Start the Local Gemma 4 26B Model Runtime

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

Test the model:

```bash
ollama run gemma4:26b
```

---

## 2. Start the Local Agent Service

Open a new terminal and run:

```bash
cd ~/Desktop/book-narrator/agent

uvicorn src.api.server:app --reload --host 127.0.0.1 --port 8000
```

Agent service URL:

```text
http://127.0.0.1:8000
```

Check Agent health:

```bash
curl http://127.0.0.1:8000/api/v1/health
```

If the health check returns successfully, the Agent service is running.

The Agent service is responsible for:

* reading PDF pages,
* preparing prompts,
* calling Gemma 4 26B,
* returning narration scripts to the backend.

---

## 3. Start the Backend

Open another terminal and run:

```bash
cd ~/Desktop/book-narrator/backend

pip install -r requirements.txt

cp .env.example .env

uvicorn app.main:app --reload --port 8080
```

Backend service URL:

```text
http://127.0.0.1:8080
```

Backend API documentation:

```text
http://127.0.0.1:8080/docs
```

Backend health check:

```bash
curl http://127.0.0.1:8080/health
```

---

## 4. Start the Frontend

Open another terminal and run:

```bash
cd ~/Desktop/book-narrator/frontend

npm install

npm run dev
```

Frontend default URL:

```text
http://127.0.0.1:5173
```

Open this URL in the browser to use the application.

---

## Environment Configuration

The backend uses a `.env` file.

Example backend `.env`:

```env
APP_NAME=gemma-book-narrator-backend
ENV=dev

CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173

DATABASE_URL=sqlite:///./app.db

AGENT_BASE_URL=http://127.0.0.1:8000

SHARED_BOOKS_DIR=~/Desktop/book-narrator/shared/books
```

The frontend can use:

```env
VITE_API_BASE_URL=http://127.0.0.1:8080
```

The Agent can use:

```env
MODEL_NAME=gemma4:26b
OLLAMA_BASE_URL=http://127.0.0.1:11434
```

or, if using an OpenAI-compatible local endpoint:

```env
OPENAI_BASE_URL=http://127.0.0.1:11434/v1
OPENAI_MODEL=gemma4:26b
```

---

## Demo Workflow

1. Open the frontend.
2. Upload a PDF file or select an existing book.
3. Choose a page range.
4. Choose a reading mode:

   * Quick Reading
   * Deep Reading
5. Choose a narration style:

   * Short-video Style
   * Bedtime Story
   * Film Commentary
6. Choose an output language:

   * English
   * Chinese
   * French
   * Spanish
   * Russian
   * Arabic
7. Choose a matching voice.
8. Add optional extra requirements, such as:

   * Analyze the main characters.
   * Analyze the writing style.
   * Summarize the key plot.
9. Click `Generate Narration`.
10. The backend creates a background task.
11. The local Agent reads the selected PDF pages.
12. Gemma 4 26B generates the narration script.
13. The backend generates audio.
14. The frontend displays the narration script and audio player.
15. The user can review previous tasks in History and save useful tasks to Favorites.

---

## Main Pages

### Home Page

The Home page introduces the project and provides entry points to:

* Create Narration
* History
* Favorites

### Create Narration Page

The Create page allows users to:

* upload a PDF,
* select an existing book,
* choose page range,
* choose reading mode,
* choose narration style,
* choose output language,
* choose voice,
* add extra task requirements,
* generate narration and audio.

### History Page

The History page allows users to:

* view previous narration tasks,
* review generated scripts,
* play audio,
* rename tasks,
* add notes,
* mark tasks as favorites,
* refill old task parameters.

### Favorites Page

The Favorites page allows users to review saved narration tasks.

---

## Main API Endpoints

### Book APIs

| Method | Path                             | Description                   |
| ------ | -------------------------------- | ----------------------------- |
| POST   | `/agent/books/upload`            | Upload a PDF book             |
| GET    | `/agent/books`                   | List uploaded books           |
| GET    | `/agent/books/{book_id}/outline` | Get PDF outline or page count |

### Narration APIs

| Method | Path                                        | Description                                |
| ------ | ------------------------------------------- | ------------------------------------------ |
| POST   | `/agent/book-narrate`                       | Create a narration task                    |
| POST   | `/agent/narration-history/{task_id}/cancel` | Cancel a running task                      |
| GET    | `/agent/narration-history`                  | List narration history                     |
| GET    | `/agent/narration-history/{task_id}`        | Get narration task detail                  |
| PUT    | `/agent/narration-history/{task_id}`        | Update task name, note, or favorite status |
| DELETE | `/agent/narration-history/{task_id}`        | Delete task history                        |

### Audio API

| Method | Path                              | Description                      |
| ------ | --------------------------------- | -------------------------------- |
| GET    | `/agent/backend-audio/{filename}` | Play or download generated audio |

### Agent API

| Method | Path                | Description        |
| ------ | ------------------- | ------------------ |
| GET    | `/api/v1/health`    | Agent health check |
| POST   | `/api/v1/agent/run` | Run Agent task     |

---

## Example Narration Request

```bash
curl -X POST http://127.0.0.1:8080/agent/book-narrate \
  -H "Content-Type: application/json" \
  -d '{
    "book_id": "example_book_id",
    "mode": "page",
    "start_page": 1,
    "end_page": 3,
    "style": "短视频",
    "reading_mode": "quick",
    "output_language": "en",
    "voice": "Ava",
    "task_instruction": "Analyze the main characters and writing style."
  }'
```

---

## Shared PDF Directory

Uploaded PDFs are saved into:

```text
~/Desktop/book-narrator/shared/books
```

This folder is important because both the backend and the local Agent service need to access the same PDF files.

If the folder does not exist, create it manually:

```bash
mkdir -p ~/Desktop/book-narrator/shared/books
```

In the recommended local demo setup, the backend and Agent run on the same machine. Therefore, the Agent can directly read uploaded PDFs from this directory.

---

## Why the Agent Runs Locally

In earlier remote-Agent setups, the backend could save a PDF to a local macOS path such as:

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

## Hackathon Relevance

This project was developed for the **Gemma 4 Good Hackathon**.

It focuses on:

* Future of Education
* Digital Equity and Inclusivity
* Multilingual learning support
* Accessible PDF understanding
* Audio-based learning.

The project demonstrates how Gemma 4 26B can help transform static documents into accessible, multilingual learning materials.

---

## Demo Submission Notes

This project is intended to be submitted as:

* a recorded demo video,
* a public GitHub repository,
* local running instructions,
* runnable project files.

The recommended demo format is:

```text
recorded demo video + public GitHub repository + local running instructions
```

This is because the system contains multiple coordinated local services and a local Gemma 4 26B model runtime.

---

## Troubleshooting

### 1. Backend Cannot Connect to Agent

Error:

```text
Connection refused: localhost:8000
```

Cause:

The backend is trying to call:

```text
http://127.0.0.1:8000/api/v1/agent/run
```

but the local Agent service is not running.

Solution:

Start the Agent service:

```bash
cd ~/Desktop/book-narrator/agent

uvicorn src.api.server:app --reload --host 127.0.0.1 --port 8000
```

Then test:

```bash
curl http://127.0.0.1:8000/api/v1/health
```

---

### 2. Gemma 4 26B Model Is Not Running

Error symptoms:

* Agent starts but generation fails.
* Model endpoint cannot be reached.
* Requests to the local model runtime fail.

Check Ollama:

```bash
ollama list
```

Start Ollama if needed:

```bash
ollama serve
```

Pull the model if missing:

```bash
ollama pull gemma4:26b
```

Test the model:

```bash
ollama run gemma4:26b
```

---

### 3. PDF File Not Found

Cause:

The Agent cannot access the PDF path sent by the backend.

Solution:

Make sure the backend and Agent are running on the same local machine and that the PDF exists in:

```text
~/Desktop/book-narrator/shared/books
```

Check:

```bash
ls -lh ~/Desktop/book-narrator/shared/books
```

---

### 4. Audio Is Not Generated

Possible causes:

* The generated script is empty.
* The generated script language does not match the selected voice.
* Edge TTS failed.
* The audio file was not saved correctly.

Check generated audio files:

```bash
ls -lh output/audio
```

---

### 5. Output Language Is Wrong

Check the Agent debug prompt if available:

```bash
cd ~/Desktop/book-narrator/agent

latest=$(ls -t debug_prompts/*.txt | head -1)

echo "$latest"

cat "$latest"
```

Confirm that the final prompt contains the expected output language requirement.

Example:

```text
The final narration script MUST be written entirely in English.
Chinese output is forbidden.
```

---

### 6. CORS Error

Make sure backend `.env` includes the frontend URL:

```env
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173
```

---

## Development Notes

SQLite is used by default.

Backend database file:

```text
app.db
```

To reset the backend database during development:

```bash
rm app.db

python -c "from app.db.base import Base; from app.db.session import engine; from app.models.narration_task import NarrationTask; Base.metadata.create_all(bind=engine)"
```

---

## Security Notice

Do not commit real passwords, API keys, server credentials, or `.env` files.

Only commit safe example files such as `.env.example`.

Recommended `.gitignore` entries:

```gitignore
.env
app.db
output/audio/
__pycache__/
*.pyc
.DS_Store
node_modules/
dist/
debug_prompts/
```

Before pushing to GitHub, check for secrets:

```bash
grep -R "PASSWORD\|API_KEY\|SECRET\|TOKEN" -n .
```

If real credentials appear, remove them before pushing.

---

## License

This project is for educational and hackathon demonstration purposes.
