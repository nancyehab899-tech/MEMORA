# MEMORA 🧠

**A smarter companion for a safer tomorrow.**

MEMORA is an AI-powered assistive system for people living with memory loss (e.g. Alzheimer's / dementia). It recognizes the people around a patient in real time and helps them recall who they are — while giving caregivers live visibility and peace of mind.

> MEMORA is an assistive technology prototype — **not** a medical device and does not diagnose or treat any condition.

---

## ✨ What it does

- **Face Recognition** — identifies people in front of the patient in real time (InsightFace + OpenCV)
- **Personal Memory Assistant** — recalls who someone is (name, relationship, shared memories, last time they met) from the patient's own stored data — never invents facts
- **Conversational AI** — answers spoken or typed questions like *"Who is this?"* or *"When did I last see her?"*, in **English, Arabic, or a natural mix of both**, powered by a local LLM via Ollama
- **Voice I/O** — speech-to-text input and text-to-speech replies
- **Location & Safe Zones** — live location tracking with safe-zone boundaries and caregiver alerts if a patient wanders
- **Caregiver Dashboard** — manage family members, schedules, safe zones, and alerts in one place
- **Patient / Caregiver roles** — separate experiences for the person living with memory loss and the people caring for them
- **Privacy-first** — face data and personal memories stay in your own database; nothing is shared beyond what you configure

---

## 🏗️ Architecture

```
MEMORA
├── code_elzehimer/          # Core Python application
│   ├── main.py               # Terminal / desktop entry point
│   ├── api_server.py         # Flask API — bridges the website to the real backend
│   ├── config.py             # Central config (DB, Ollama, language, schedule refresh)
│   ├── ai/
│   │   └── assistant.py      # Assistant class — single AI pipeline for text & voice
│   ├── database/
│   │   ├── facedatabase.py       # Face embeddings storage & matching
│   │   ├── patient_profile.py    # Patient identity & family data
│   │   ├── patient_memory.py     # Schedule / facts / memories
│   │   ├── safe_zone_store.py    # Safe zone persistence
│   │   ├── caregiver_auth.py     # Caregiver/admin login
│   │   └── db_connection.py
│   ├── recognition/
│   │   └── face_recognizer.py    # InsightFace-based recognition pipeline
│   ├── vision/
│   │   ├── face_capture.py
│   │   └── face_detector.py
│   ├── voice/
│   │   ├── speech_to_text.py
│   │   ├── text_to_speech.py
│   │   └── alarm.py
│   ├── location/
│   │   ├── gps_tracker.py        # Live GPS (Windows, via winsdk)
│   │   ├── safe_zone.py
│   │   └── map_setup.py
│   ├── language_utils.py     # Language auto-detection (EN/AR)
│   ├── setup_wizard.py       # First-run family & face setup
│   └── voice_assistant.py    # Terminal voice interaction loop
│
└── website/                  # Real web front end (talks to api_server.py)
    ├── index.html
    ├── style.css              # Dark neon / cyberpunk theme (glass cards, neon glow)
    ├── script.js               # Talks to the real backend over HTTP
    ├── bg.jpg / hero.jpg
    └── api_server.py           # Copy of the Flask bridge (also lives in code_elzehimer/)
```

**How it fits together:** the website is a real front end, not a demo — `script.js` calls `api_server.py` (Flask), which uses the *actual* `database/`, `recognition/`, and `ai/assistant.py` modules from the Python project. Same SQLite/MySQL database, same InsightFace pipeline, same local Ollama model as the terminal app.

---

## 🧰 Tech stack

| Layer | Technology |
|---|---|
| Computer vision | OpenCV, InsightFace |
| AI assistant | Ollama (local LLM, e.g. `llama3.2`) |
| Backend API | Python, Flask |
| Database | SQLite (default) or MySQL |
| Voice | Web Speech API (browser STT/TTS) |
| Web front end | HTML, CSS, vanilla JS, Leaflet (maps) |
| Location | Browser geolocation / device GPS (`winsdk` on Windows) |

---

## 🚀 Getting started

### 1. Install dependencies
From the `code_elzehimer/` folder:
```bash
pip install opencv-python insightface ollama flask
```
Make sure [Ollama](https://ollama.com) is installed and running locally, with your chosen model pulled (default: `llama3.2`):
```bash
ollama pull llama3.2
```

### 2. Configure
Edit `config.py` to set your database (SQLite is used out of the box; MySQL credentials are also configurable) and Ollama settings.

### 3. Run the backend
```bash
python api_server.py
```
You should see:
```
MEMORA API running on http://127.0.0.1:5000
```

### 4. Run the website
Open the `website/` folder in VS Code, install the **Live Server** extension, then right-click `index.html` → **Open with Live Server**.
(Don't open `index.html` directly — browsers block camera access on `file://` pages.)

If the backend and website run on different machines/ports, update:
```html
<script>window.MEMORA_API_BASE = 'http://127.0.0.1:5000/api';</script>
```

### Or: run the terminal app directly
```bash
python main.py
```

---

## 🖥️ Using MEMORA

- **Patients** sign up/log in, register their face, and can ask the AI assistant questions naturally — text or voice, in English or Arabic.
- **Caregivers** log in via the caregiver dashboard to manage family members, review the patient's schedule, set safe zones on the map, and receive alerts.
- The **camera tab** recognizes known faces automatically and lets you register new ones on the spot ("Add them now").

Default caregiver/admin login (change before real-world use): `NanZy@memora.NZ` / `NanZy`

---

## 🔒 Privacy & safety notes

- Face embeddings and personal memories are stored in your own database — nothing leaves your machine unless you deploy it that way.
- The AI assistant only surfaces facts that were actually stored for the logged-in patient — it does not fabricate relationships or memories.
- CORS is wide open by default for local development — **tighten this before exposing the server beyond your own machine.**
- This project is a prototype and assistive aid, not a certified medical device.

---

## 🗺️ Status

Actively in development. Recent additions include a landing-page nav that tracks scroll position, patient photo capture at signup/login, an AI assistant that's aware of the patient's family and daily schedule, and a 24-hour schedule refresh cycle.

---

