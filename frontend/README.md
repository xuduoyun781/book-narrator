# Gemma Book Narrator Frontend

This is the Vue frontend for **Gemma Book Narrator Agent**, a multilingual PDF-to-narration and audio learning assistant powered by **Gemma 4 26B**.

The frontend provides the user interface for uploading PDFs, creating narration tasks, selecting output languages, playing generated audio, and managing narration history and favorites.

---

## Role in the System

The frontend is the user-facing part of the project.

It communicates with the local FastAPI backend through HTTP APIs. The frontend does not directly call Gemma 4. Instead, it sends user settings and task parameters to the backend. The backend then calls the local Agent service, and the Agent uses **Gemma 4 26B** to generate narration scripts.

```text
Vue Frontend
    |
    | HTTP API
    v
FastAPI Backend
    |
    | Calls local Agent
    v
Local Gemma 4 Agent Service
    |
    | Calls local Gemma 4 26B model
    v
Generated narration script and audio
```

---

## Main Features

* Home page with project introduction.
* PDF upload interface.
* Book selection from uploaded files.
* Page-range narration task creation.
* Reading mode selection:

  * Quick Reading
  * Deep Reading
* Narration style selection:

  * Short-video style
  * Bedtime story style
  * Film commentary style
* Output language selection:

  * English
  * Chinese
  * French
  * Spanish
  * Russian
  * Arabic
* Voice selection matching the selected output language.
* Extra task requirement input.
* Narration script display.
* Audio playback.
* Task status polling.
* Task cancellation.
* History page.
* Favorites page.
* Reuse of previous task parameters.

---

## Technology Stack

| Component   | Technology |
| ----------- | ---------- |
| Framework   | Vue        |
| Build Tool  | Vite       |
| Language    | TypeScript |
| HTTP Client | Axios      |
| Routing     | Vue Router |
| Styling     | Scoped CSS |

---

## Recommended Local Directory Layout

For the local demo, the frontend is expected to run together with the backend, Agent service, and shared PDF folder on the same machine.

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

The frontend communicates only with the backend:

```text
http://127.0.0.1:8080
```

The backend communicates with the local Agent service:

```text
http://127.0.0.1:8000
```

Uploaded PDFs are saved by the backend into:

```text
~/Desktop/book-narrator/shared/books
```

Because the backend and Agent run locally on the same machine, the Agent can directly read uploaded PDFs from this shared directory.

---

## Project Structure

```text
frontend/
├── src/
│   ├── api/
│   │   ├── request.ts
│   │   ├── bookNarrator.ts
│   │   └── narrationHistory.ts
│   ├── pages/
│   │   ├── HomePage.vue
│   │   ├── BookNarratorPage.vue
│   │   ├── NarrationHistoryPage.vue
│   │   └── FavoritesPage.vue
│   ├── router/
│   │   └── index.ts
│   ├── App.vue
│   └── main.ts
├── public/
├── package.json
├── vite.config.ts
└── README.md
```

---

## API Files

### `src/api/request.ts`

Creates the Axios instance and defines the backend base URL.

Default backend URL:

```text
http://127.0.0.1:8080
```

The frontend can also use the environment variable:

```env
VITE_API_BASE_URL=http://127.0.0.1:8080
```

---

### `src/api/bookNarrator.ts`

Contains APIs for:

* uploading PDF books,
* listing books,
* getting book outline or page count,
* creating narration tasks,
* cancelling running narration tasks.

Main endpoints:

```text
POST /agent/books/upload
GET  /agent/books
GET  /agent/books/{book_id}/outline
POST /agent/book-narrate
POST /agent/narration-history/{task_id}/cancel
```

---

### `src/api/narrationHistory.ts`

Contains APIs for:

* listing narration history,
* getting task details,
* updating task name, notes, or favorite status,
* deleting history tasks.

Main endpoints:

```text
GET    /agent/narration-history
GET    /agent/narration-history/{task_id}
PUT    /agent/narration-history/{task_id}
DELETE /agent/narration-history/{task_id}
```

---

## Main Pages

### Home Page

The Home page introduces the project and provides entry points to:

* Create Narration
* History
* Favorites

It is designed as the first landing page for the demo.

---

### Create Narration Page

The Create Narration page is the main workspace.

Users can:

* upload a PDF,
* select an existing uploaded book,
* choose page range,
* choose reading mode,
* choose narration style,
* choose output language,
* choose voice,
* enter extra task requirements,
* generate narration,
* view generation status,
* play generated audio,
* copy generated narration script.

The page also supports background task polling. After a task is created, the frontend periodically requests the backend for task status until the task is completed, failed, or cancelled.

---

### History Page

The History page allows users to:

* view previous narration tasks,
* search history,
* view generated scripts,
* play audio,
* rename tasks,
* add notes,
* mark tasks as favorites,
* refill previous task parameters into the Create page,
* delete history records.

This page helps make generated learning materials reusable.

---

### Favorites Page

The Favorites page shows narration tasks marked as favorites.

Users can:

* review important tasks,
* play audio,
* view scripts,
* rename tasks,
* remove tasks from favorites.

---

## Output Languages

The frontend supports the following output languages:

* English
* Chinese
* French
* Spanish
* Russian
* Arabic

The selected output language is sent to the backend as:

```json
{
  "output_language": "en"
}
```

Supported language codes:

| Language | Code |
| -------- | ---- |
| English  | `en` |
| Chinese  | `zh` |
| French   | `fr` |
| Spanish  | `es` |
| Russian  | `ru` |
| Arabic   | `ar` |

---

## Voice Selection

The selected voice should match the selected output language.

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

## Environment Configuration

Create a `.env` file in the frontend directory if needed.

```env
VITE_API_BASE_URL=http://127.0.0.1:8080
```

If this variable is not set, the frontend should use the default backend URL:

```text
http://127.0.0.1:8080
```

---

## Install Dependencies

```bash
cd ~/Desktop/book-narrator/frontend

npm install
```

---

## Start the Frontend

```bash
cd ~/Desktop/book-narrator/frontend

npm run dev
```

Default frontend URL:

```text
http://127.0.0.1:5173
```

Open this URL in the browser to use the application.

---

## Required Services Before Running the Frontend

Before using the frontend, make sure the local Gemma 4 model runtime, Agent service, and backend service are running.

---

### 1. Start the Local Gemma 4 Model Runtime

If using Ollama:

```bash
ollama serve
```

If Ollama is already running in the background, this step may not be necessary.

Make sure the model exists:

```bash
ollama list
```

The intended model is:

```text
gemma4:26b
```

If missing:

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

---

### 3. Start the Backend Service

```bash
cd ~/Desktop/book-narrator/backend

uvicorn app.main:app --reload --port 8080
```

Check backend health:

```bash
curl http://127.0.0.1:8080/health
```

---

## Demo Workflow

1. Open the frontend at:

```text
http://127.0.0.1:5173
```

2. Go to the Create Narration page.

3. Upload a PDF file or select an existing book.

4. Choose a page range.

5. Select reading mode:

   * Quick Reading
   * Deep Reading

6. Select narration style:

   * Short-video Style
   * Bedtime Story
   * Film Commentary

7. Select output language:

   * English
   * Chinese
   * French
   * Spanish
   * Russian
   * Arabic

8. Select a matching voice.

9. Add optional extra task requirements, such as:

```text
Analyze the main characters and writing style.
```

10. Click `Generate Narration`.

11. Wait for the background task to complete.

12. Review the generated narration script.

13. Play the generated audio.

14. Open the History page to review previous tasks.

15. Mark useful tasks as favorites.

---

## Example Payload Sent to Backend

When a user creates a narration task, the frontend sends a request similar to:

```json
{
  "task_name": "My narration task",
  "book_id": "example_book_id",
  "style": "Short-video Style",
  "voice": "Ava",
  "reading_mode": "quick",
  "output_language": "en",
  "mode": "page",
  "start_page": 1,
  "end_page": 3,
  "task_instruction": "Analyze the main characters and writing style."
}
```

The backend then sends the relevant PDF path and task settings to the local Agent service.

---

## Local Shared PDF Directory

The frontend uploads PDF files through the backend.

The backend saves uploaded PDFs into:

```text
~/Desktop/book-narrator/shared/books
```

Because the backend and Agent run locally on the same machine, the Agent can directly read the PDF files from this directory.

If the folder does not exist, create it manually:

```bash
mkdir -p ~/Desktop/book-narrator/shared/books
```

---

## Notes for the Hackathon Demo

This frontend is part of a local runnable demo for the Gemma 4 Good Hackathon.

The demo is designed to show:

* PDF upload,
* page-range narration,
* Gemma 4 26B generation,
* multilingual narration output,
* audio playback,
* history management,
* favorites management.

The frontend demonstrates the complete user workflow and shows how Gemma 4 can help transform long PDF documents into accessible, multilingual learning materials.

---

## Troubleshooting

### 1. Frontend Cannot Connect to Backend

Error symptoms:

* API request failed.
* Book list cannot load.
* Upload fails.
* Generate button returns backend connection error.

Check whether backend is running:

```bash
curl http://127.0.0.1:8080/health
```

If it fails, start backend:

```bash
cd ~/Desktop/book-narrator/backend

uvicorn app.main:app --reload --port 8080
```

---

### 2. Backend Cannot Connect to Agent

Error symptoms shown in frontend:

```text
Connection refused: localhost:8000
```

Cause:

The local Agent service is not running.

Start Agent:

```bash
cd ~/Desktop/book-narrator/agent

uvicorn src.api.server:app --reload --host 127.0.0.1 --port 8000
```

Check Agent health:

```bash
curl http://127.0.0.1:8000/api/v1/health
```

---

### 3. Gemma 4 26B Model Is Not Running

Error symptoms:

* Agent starts but generation fails.
* The local model runtime cannot find `gemma4:26b`.
* Model calls fail.

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

### 4. No Audio Is Displayed

Possible causes:

* The backend failed to generate audio.
* The script language does not match the selected voice.
* The audio URL is empty.
* The backend audio route is unavailable.

Check backend logs and verify generated audio files:

```bash
cd ~/Desktop/book-narrator/backend

ls -lh output/audio
```

---

### 5. Uploaded PDF Does Not Appear

Check backend shared directory:

```bash
ls -lh ~/Desktop/book-narrator/shared/books
```

If the PDF is missing, upload it again through the frontend.

---

### 6. Output Language Is Wrong

The frontend sends `output_language` to the backend.

If the result language is wrong, check:

1. The selected output language in the frontend.
2. The selected voice.
3. Backend logs.
4. Agent debug prompts, if available.

The final Agent prompt should contain the selected output language requirement.

---

## Build for Production

```bash
npm run build
```

The production build output is usually generated in:

```text
dist/
```

Preview the production build locally:

```bash
npm run preview
```

---

## Security Notes

Do not commit environment files containing real credentials.

Recommended `.gitignore` entries:

```gitignore
.env
node_modules/
dist/
.DS_Store
```

---

## License

This frontend is for educational and hackathon demonstration purposes.
