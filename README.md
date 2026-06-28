# J.A.R.V.I.S

**Just A Rather Very Intelligent System** — a locally-running AI voice assistant inspired by Iron Man's JARVIS, built for Windows.

## Overview

J.A.R.V.I.S uses a **dual-ear architecture** to stay efficient:

- **Sleep mode** — uses Vosk (lightweight, offline) to listen for the wake word "JARVIS" at near-zero CPU/GPU cost.
- **Active mode** — once triggered, switches to OpenAI Whisper for high-accuracy speech transcription.
- **Brain** — routes commands to local Llama 3.2 via Ollama for natural language responses.
- **Voice** — responds using Windows SAPI5 TTS via pyttsx3.

Everything runs **100% locally** — no cloud APIs required.

## Architecture

```
You speak "JARVIS"
       │
       ▼
[WATCHMAN] Vosk wake-word detector (always-on, low power)
       │
       ▼
[EARS] Whisper base.en transcribes your command
       │
       ▼
[MAIN] Routes to REFLEX or BRAIN
   ├── Web search   → body.search_web()
   ├── Time query   → body.get_time()
   ├── Open app     → body.open_app()
   ├── System ctrl  → body.system_control()
   └── Everything else → Ollama (Llama 3.2)
       │
       ▼
[MOUTH] pyttsx3 speaks the reply
       │
       ▼
[Zzz] Back to sleep, waiting for wake word
```

## Features

| Feature | How |
|---|---|
| Wake word detection | Vosk (offline, low CPU) |
| Speech-to-text | OpenAI Whisper `base.en` |
| AI responses | Ollama + Llama 3.2 (local) |
| Text-to-speech | Windows SAPI5 via pyttsx3 |
| Open apps | Calculator, Notepad, CMD |
| System control | Volume up/down/mute, screenshots |
| Web/YouTube search | Opens browser with query |
| Time queries | Reads system clock |

## Requirements

- Python 3.10+
- [Ollama](https://ollama.com) with `llama3.2` pulled
- [Vosk small English model](https://alphacephei.com/vosk/models) — extract to `model/` folder
- A working microphone

### Install dependencies

```bash
pip install ollama openai-whisper speechrecognition vosk sounddevice pyttsx3 pyautogui
```

> **Note:** `pyaudio` is required by `speechrecognition`. On Windows, install it via:
> ```bash
> pip install pipwin && pipwin install pyaudio
> ```

### Set up the Vosk model

Download `vosk-model-small-en-us-0.15` from [alphacephei.com/vosk/models](https://alphacephei.com/vosk/models) and extract it to a folder named `model/` in the project root.

## Usage

```bash
python main.py
```

Say **"JARVIS"** to wake it up, then speak your command.

Say **"stop"** or **"exit"** to shut down.

## Project Structure

```
J.A.R.V.I.S/
├── main.py        # Orchestrator — wake → listen → think → speak loop
├── watchman.py    # Wake word detection (Vosk, always-on)
├── ears.py        # Speech-to-text (Whisper)
├── brain.py       # LLM via Ollama (Llama 3.2)
├── mouth.py       # Text-to-speech (pyttsx3 / SAPI5)
├── body.py        # System actions (apps, volume, screenshots)
├── model/         # Vosk model files (not tracked in git)
└── venv/          # Python virtual environment
```

## Notes

- Designed and tested on Windows 11 with a Lenovo LOQ laptop.
- The `brain.py` file contains an alternate LM Studio backend (not used in `main.py`).
- Whisper writes a temporary `temp_input.wav` for transcription — this is cleaned up between sessions.

## License

MIT
