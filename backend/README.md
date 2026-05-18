# Gemma Book Narrator Backend

This is the FastAPI backend service for **Gemma Book Narrator Agent**, a multilingual PDF-to-narration and audio learning assistant powered by **Gemma 4 26B**.

The backend receives PDF uploads from the frontend, saves uploaded books into a shared local directory, creates narration tasks, calls the local Agent service, stores task history, generates audio files, and serves results back to the frontend.

---

## Role in the System

The backend is the coordination layer between the frontend and the local Gemma 4 Agent service.

```text
Vue Frontend
    |
    | HTTP API
    v
FastAPI Backend
    |
    | Save uploaded PDFs
    v
~/Desktop/book-narrator/shared/books
    |
    | Send PDF path and task settings
    v
Local Agent Service
    |
    | Calls Gemma 4 26B
    v
Generated narration script
    |
    v
FastAPI Backend
    |
    | Save script
    | Generate audio
    | Store task history
    v
Vue Frontend
```

The frontend does not directly call the Agent or Gemma 4.  
The frontend only communicates with this backend service.

The backend communicates with the local Agent service through:

```text
http://127.0.0.1:8000
```

---

## Main Responsibilities

- Receive uploaded PDF files from the frontend.
- Automatically create and use the shared book directory.
- Save uploaded PDFs into `~/Desktop/book-narrator/shared/books`.
- List uploaded books.
- Get PDF outline or page count.
- Create narration tasks.
- Send PDF path, page range, reading mode, narration style, output language, voice, and user requirements to the local Agent service.
- Receive generated narration script from the Agent.
- Generate audio from the final script.
- Store narration task history in SQLite.
- Support task status polling.
- Support task cancellation.
- Support task renaming, notes, and favorites.
- Serve generated audio files to the frontend.

---

## Technology Stack

| Component | Technology |
| --- | --- |
| Web Framework | FastAPI |
| Database | SQLite |
| ORM | SQLAlchemy |
| Agent Client | Requests |
| Audio Generation | Edge TTS |
| Config Management | `.env` + pydantic-settings |
| API Docs | Swagger / OpenAPI |

---

## Recommended Local Directory Layout

For the local demo, the frontend, backend, Agent service, and shared PDF directory should be placed under the same project folder.

```text
~/Desktop/book-narrator/
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

The backend saves uploaded PDFs into:

```text
~/Desktop/book-narrator/shared/books
```

Because the Agent service also runs locally, it can directly read the same PDF files from this directory.

---

## Project Structure

```text
backend/
├── app/
│   ├── api/
│   │   └── routes/
│   │       ├── agent.py              # Book upload, narration task, history, audio APIs
│   │       └── meta.py               # Health check
│   ├── core/
│   │   └── config.py                 # Global configuration
│   ├── db/
│   │   ├── base.py                   # SQLAlchemy Base
│   │   └── session.py                # Database session
│   ├── models/
│   │   └── narration_task.py         # Narration task ORM model
│   ├── schemas/
│   │   └── narration_task.py         # Request and response schemas
│   ├── services/
│   │   ├── agent_client.py           # Calls the local Agent service
│   │   └── audio_service.py          # Generates backend audio files
│   └── main.py                       # FastAPI app entry
├── output/
│   └── audio/                        # Generated audio files
├── .env.example
├── requirements.txt
└── README.md
```

---

## Environment Variables

Create a `.env` file from `.env.example`.

```bash
cp .env.example .env
```

Recommended local `.env`:

```env
APP_NAME=gemma-book-narrator-backend
ENV=dev

# CORS
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173

# Database
DATABASE_URL=sqlite:///./app.db

# Local Agent service
AGENT_BASE_URL=http://127.0.0.1:8000

# Shared PDF directory
SHARED_BOOKS_DIR=~/Desktop/book-narrator/shared/books
```

The backend expects the local Agent service to run at:

```text
http://127.0.0.1:8000
```

The frontend expects the backend to run at:

```text
http://127.0.0.1:8080
```

---

## `.env.example`

A safe `.env.example` file can look like this:

```env
APP_NAME=gemma-book-narrator-backend
ENV=dev

# CORS
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173

# Database
DATABASE_URL=sqlite:///./app.db

# Local Agent service
AGENT_BASE_URL=http://127.0.0.1:8000

# Shared PDF directory
SHARED_BOOKS_DIR=~/Desktop/book-narrator/shared/books
```

Do not include real passwords, API keys, tokens, or server credentials in `.env.example`.

---

## Install Dependencies

```bash
cd ~/Desktop/book-narrator/backend

pip install -r requirements.txt
```

---

## Start the Backend

Before starting the backend, make sure the local Gemma 4 26B model runtime and the local Agent service are already running.

Start backend:

```bash
cd ~/Desktop/book-narrator/backend

uvicorn app.main:app --reload --port 8080
```

Backend service URL:

```text
http://127.0.0.1:8080
```

Swagger API documentation:

```text
http://127.0.0.1:8080/docs
```

Backend health check:

```bash
curl http://127.0.0.1:8080/health
```

---

## Required Services Before Running the Backend

The backend depends on the local Agent service. The Agent service depends on the local Gemma 4 26B model runtime.

---

### 1. Start the Local Gemma 4 26B Model Runtime

If using Ollama:

```bash
ollama serve
```

If Ollama is already running in the background, this step may not be necessary.

Make sure the model is available:

```bash
ollama list
```

The intended model is:

```text
gemma4:26b
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

### 2. Start the Local Agent Service

```bash
cd ~/Desktop/book-narrator/agent

uvicorn src.api.server:app --reload --host 127.0.0.1 --port 8000
```

Check Agent health:

```bash
curl http://127.0.0.1:8000/api/v1/health
```

If the Agent health check succeeds, the backend can call the Agent service.

---

## Local Gemma 4 26B Usage

This backend does not directly call Gemma 4 26B.

Instead, it sends narration requests to the local Agent service:

```text
http://127.0.0.1:8000/api/v1/agent/run
```

The Agent service is responsible for:

- reading selected PDF pages,
- extracting source text,
- building prompts,
- applying output language requirements,
- calling local `gemma4:26b`,
- returning the final narration script.

The backend then receives the generated script and produces audio.

---

## Shared Books Directory

Uploaded PDFs are saved into:

```text
~/Desktop/book-narrator/shared/books
```

Example on macOS:

```text
/Users/your-name/Desktop/book-narrator/shared/books
```

The backend should automatically create this directory when a PDF is uploaded.

If needed, create it manually:

```bash
mkdir -p ~/Desktop/book-narrator/shared/books
```

This directory is important because both the backend and local Agent service need to access the same PDF files.

---

## Why the Agent Runs Locally

In a remote-Agent setup, the backend may save a PDF to a local macOS path such as:

```text
/Users/your-name/Desktop/shared/books/example.pdf
```

A cloud Agent cannot read that local path.

To avoid this file path problem, this demo runs the Agent locally on the same machine as the backend.

This makes the PDF workflow simple and reliable:

```text
Frontend -> Local Backend -> ~/Desktop/book-narrator/shared/books -> Local Agent -> Local Gemma 4 26B
```

---

## Main API Endpoints

### Book APIs

| Method | Path | Description |
| --- | --- | --- |
| POST | `/agent/books/upload` | Upload a PDF book |
| GET | `/agent/books` | List uploaded PDF books |
| GET | `/agent/books/{book_id}/outline` | Get PDF outline or page count |

---

### Narration APIs

| Method | Path | Description |
| --- | --- | --- |
| POST | `/agent/book-narrate` | Create a narration task |
| POST | `/agent/narration-history/{task_id}/cancel` | Cancel a running task |
| GET | `/agent/narration-history` | List narration history |
| GET | `/agent/narration-history/{task_id}` | Get narration task detail |
| PUT | `/agent/narration-history/{task_id}` | Update task name, note, or favorite status |
| DELETE | `/agent/narration-history/{task_id}` | Delete narration history |

---

### Audio API

| Method | Path | Description |
| --- | --- | --- |
| GET | `/agent/backend-audio/{filename}` | Play or download generated audio |

---

### Health Check

| Method | Path | Description |
| --- | --- | --- |
| GET | `/health` | Backend health check |

---

## Example PDF Upload Request

```bash
curl -X POST http://127.0.0.1:8080/agent/books/upload \
  -F "file=@/path/to/example.pdf"
```

After upload, the file should appear in:

```text
~/Desktop/book-narrator/shared/books
```

Check:

```bash
ls -lh ~/Desktop/book-narrator/shared/books
```

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
    "style": "Short-video Style",
    "reading_mode": "quick",
    "output_language": "en",
    "voice": "Ava",
    "task_instruction": "Analyze the main characters and writing style."
  }'
```

---

## Narration Task Flow

When the frontend creates a narration task, the backend performs the following steps:

1. Receive task settings from the frontend.
2. Find the selected PDF file from `shared/books`.
3. Build the Agent request.
4. Send the request to the local Agent service.
5. Receive the generated narration script.
6. Generate audio with Edge TTS.
7. Save script, audio URL, and metadata into SQLite.
8. Return task status and result to the frontend.

---

## Reading Modes

The backend forwards the selected reading mode to the Agent.

### Quick Reading

Quick Reading focuses on:

- main characters,
- key events,
- important turning points,
- main conflict,
- visible outcomes.

It compresses secondary descriptions and avoids overly long analysis.

### Deep Reading

Deep Reading keeps more details and supports:

- richer event development,
- more complete character relationships,
- writing style analysis,
- character analysis,
- deeper learning-oriented interpretation.

---

## Narration Styles

The backend forwards the selected narration style to the Agent.

Supported styles include:

- Short-video style
- Bedtime story style
- Film commentary style

The style affects the tone, pacing, and structure of the generated narration.

---

## Output Language Support

The backend supports the following output languages:

- English
- Chinese
- French
- Spanish
- Russian
- Arabic

The selected output language is sent to the Agent and should be reflected in the final narration script.

Example payload field:

```json
{
  "output_language": "en"
}
```

Supported language codes:

| Language | Code |
| --- | --- |
| English | `en` |
| Chinese | `zh` |
| French | `fr` |
| Spanish | `es` |
| Russian | `ru` |
| Arabic | `ar` |

---

## Voice Mapping

The selected voice should match the output language.

| Output Language | Suggested Voices |
| --- | --- |
| English | Ava, Jenny, Guy, Brian |
| Chinese | Xiaoxiao, Yunxi, Yunyang, Xiaoyi |
| French | Denise, Henri |
| Spanish | Elvira, Alvaro |
| Russian | Svetlana, Dmitry |
| Arabic | Zariyah, Hamed |

If the generated script language does not match the selected voice language, audio generation may fail or sound unnatural.

---

## Audio Generation

The backend uses Edge TTS to generate audio from the final narration script.

Generated audio files are saved into:

```text
output/audio/
```

Example:

```text
output/audio/task_id_en.mp3
```

The frontend accesses generated audio through:

```text
/agent/backend-audio/{filename}
```

Check audio output:

```bash
ls -lh output/audio
```

---

## Database

SQLite is used by default.

Database file:

```text
app.db
```

The narration task table stores:

- task ID,
- task name,
- book information,
- page range,
- reading mode,
- narration style,
- output language,
- voice,
- task instruction,
- generated script,
- audio URLs,
- status,
- error message,
- notes,
- favorite status,
- timestamps.

---

## Reset Database in Development

To reset the database during development:

```bash
rm app.db

python -c "from app.db.base import Base; from app.db.session import engine; from app.models.narration_task import NarrationTask; Base.metadata.create_all(bind=engine)"
```

---

## Frontend Connection

The frontend should call this backend through:

```env
VITE_API_BASE_URL=http://127.0.0.1:8080
```

Make sure CORS is configured in backend `.env`:

```env
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173
```

---

## Troubleshooting

### 1. Backend Cannot Connect to Agent

Error:

```text
Connection refused: localhost:8000
```

Cause:

The backend is trying to call the local Agent service, but the Agent is not running.

Solution:

Start the Agent:

```bash
cd ~/Desktop/book-narrator/agent

uvicorn src.api.server:app --reload --host 127.0.0.1 --port 8000
```

Test:

```bash
curl http://127.0.0.1:8000/api/v1/health
```

---

### 2. Gemma 4 26B Model Is Not Running

Error symptoms:

- Agent starts but generation fails.
- Model endpoint cannot be reached.
- Requests to the local model runtime fail.

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

### 3. Uploaded PDF Does Not Appear

Check the shared directory:

```bash
ls -lh ~/Desktop/book-narrator/shared/books
```

If the folder does not exist:

```bash
mkdir -p ~/Desktop/book-narrator/shared/books
```

Then upload the PDF again from the frontend.

---

### 4. PDF File Not Found by Agent

Cause:

The Agent cannot access the PDF path sent by the backend.

Solution:

Make sure backend and Agent are running on the same local machine and the PDF exists in:

```text
~/Desktop/book-narrator/shared/books
```

Check:

```bash
ls -lh ~/Desktop/book-narrator/shared/books
```

---

### 5. No Audio Generated

Possible causes:

- The generated script is empty.
- The script language does not match the selected voice.
- Edge TTS failed.
- The audio file was not saved.

Check audio files:

```bash
ls -lh output/audio
```

Check backend logs for audio generation errors.

---

### 6. Output Language Is Wrong

Check:

1. The frontend selected output language.
2. The selected voice.
3. Backend logs.
4. Agent debug prompt.
5. Final narration script language.

The Agent prompt should include the selected output language requirement.

---

### 7. CORS Error

Make sure `.env` includes:

```env
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173
```

Then restart the backend.

---

## Local Demo Workflow

Recommended startup order:

1. Start Ollama or the local model runtime.
2. Make sure `gemma4:26b` is available.
3. Start the local Agent service.
4. Start the backend service.
5. Start the frontend service.
6. Upload or select a PDF.
7. Generate narration.
8. Review script and audio.
9. Check history and favorites.

---

## Hackathon Relevance

This backend is part of the **Gemma Book Narrator Agent** project for the Gemma 4 Good Hackathon.

The project focuses on:

- Future of Education
- Digital Equity and Inclusivity
- Multilingual learning support
- Accessible PDF understanding
- Audio-based learning.

The backend makes the project a complete runnable system by connecting user interaction, PDF storage, Agent generation, audio output, and task history.

---

## Security Notes

Do not commit real passwords, API keys, server credentials, tokens, or `.env` files.

Only commit safe example files such as `.env.example`.

Recommended `.gitignore` entries:

```gitignore
.env
app.db
output/audio/
__pycache__/
*.pyc
.DS_Store
```

Before pushing to GitHub, check for secrets:

```bash
grep -R "PASSWORD\|API_KEY\|SECRET\|TOKEN" -n .
```

If real credentials appear, remove them before pushing.

---

## License

This backend is for educational and hackathon demonstration purposes.
