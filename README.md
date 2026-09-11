# Context-Aware AI Desktop Assistant

A translucent, always-on-top desktop overlay that understands what is on your screen and responds to it. Capture any region of your display, record a voice note, or ask a question directly — the assistant analyses the context and returns an explanation, a summary, or a set of suggested next actions, without ever taking over the window you are working in.

Built with **Electron + React + TypeScript**, with a pluggable inference layer that runs against either **Google Gemini** (cloud) or **Ollama** (fully local).

---

## Key Features

**Unobtrusive overlay interface**
Frameless, transparent, always-on-top window that collapses to a single command bar when idle. Hidden from the taskbar and excluded from screen capture, so it never appears in recordings or shared screens.

**Screen understanding**
Capture the screen with a global hotkey and have the content analysed automatically — diagrams, error messages, documents, slides or code. The capture queue holds the five most recent images and evicts older ones automatically.

**Voice input**
Record directly from the command bar. Audio is transcribed and summarised, with suggested follow-up actions — useful for meeting notes and quick dictation.

**Contextual chat**
Ask follow-up questions in a panel that opens beneath the bar and closes when you are done. The window resizes itself to fit whatever is currently displayed.

**Switchable AI provider**
Choose between cloud and local inference at runtime from the **Models** panel — no restart, no code change. A built-in connection test verifies the provider before you rely on it.

**Privacy by design**
With a local model, no screen or audio data leaves the machine. Captures are stored in the application's own data directory and deleted when the queue is reset.

**Cross-platform**
Windows 10/11, macOS and major Linux distributions, with platform-specific window handling for each.

---

## Requirements

- **Node.js** 18 or newer
- **One** of the following inference backends:
  - A **Gemini API key** from Google AI Studio, or
  - **Ollama** installed locally (recommended if you want everything to stay on your machine)

**Hardware**

| | RAM | CPU | Storage |
|---|---|---|---|
| Minimum | 4 GB | Dual-core | 2 GB |
| Recommended | 8 GB | Quad-core | 5 GB |
| Local models | 16 GB+ | Quad-core | 10 GB+ |

---

## Setup

**1. Install dependencies**

```bash
npm install
```

If the image-processing dependency fails to build (a `gyp ERR! find Python` message), use the prebuilt binaries instead:

```bash
SHARP_IGNORE_GLOBAL_LIBVIPS=1 npm install --ignore-scripts
npm rebuild sharp
```

**2. Configure the inference provider**

Create a `.env` file in the project root.

For cloud inference:

```env
GEMINI_API_KEY=your_api_key_here
```

For local inference:

```env
USE_OLLAMA=true
OLLAMA_MODEL=llama3.2
OLLAMA_URL=http://localhost:11434
```

If `OLLAMA_MODEL` is omitted, the first locally available model is detected and used automatically.

**3. Run**

```bash
npm start
```

This starts the Vite dev server on port 5180, waits for it to become available, and launches the Electron shell.

To produce a distributable build:

```bash
npm run dist
```

The packaged application is written to the `release` folder.

---

## Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl/Cmd + Shift + Space` | Show and centre the overlay |
| `Ctrl/Cmd + B` | Toggle visibility |
| `Ctrl/Cmd + H` | Capture the screen |
| `Ctrl/Cmd + Enter` | Analyse the current capture |
| `Ctrl/Cmd + R` | Cancel processing and clear the queue |
| `Ctrl/Cmd + ← ↑ ↓ →` | Move the overlay |
| `Ctrl/Cmd + Q` | Quit |

---

## Choosing a Provider

**Local (Ollama)**
Nothing leaves the machine, there are no usage costs, and it works offline. Supports `llama3.2`, `mistral`, `codellama` and any other Ollama-compatible model. Requires more local memory and is slower than cloud inference on modest hardware.

Install Ollama, pull a model, and make sure the service is running:

```bash
ollama pull llama3.2
ollama serve
```

**Cloud (Gemini)**
Faster, stronger on complex reasoning, and supports image and audio understanding out of the box. Requires an internet connection and an API key, and sends the captured content to the provider.

---

## Project Structure

```
electron/          Main process
  main.ts            Application state façade and lifecycle
  WindowHelper.ts    Overlay geometry, transparency, positioning
  ScreenshotHelper.ts  Capture, storage and queue management
  ShortcutsHelper.ts   Global hotkey registration
  ProcessingHelper.ts  Analysis pipeline orchestration
  LLMHelper.ts         Provider abstraction and prompting
  ipcHandlers.ts       Inter-process channel wiring
  preload.ts           Typed, sandboxed bridge to the interface

src/               Renderer process (React)
  App.tsx            View routing and event subscriptions
  _pages/            Queue, Solutions, Debug
  components/        Command bar, capture list, model selector, UI kit
  types/             Shared data contracts

worker-script/     Background thread for offloaded work
```

The renderer runs with context isolation enabled and has no direct access to Node or the operating system; it communicates with the main process exclusively through the typed bridge exposed in `preload.ts`.

---

## Troubleshooting

**The application does not start.**
Confirm nothing else is bound to port 5180:

```bash
lsof -i :5180     # macOS / Linux
netstat -ano | findstr :5180   # Windows
```

**Local inference fails to connect.**
Confirm the Ollama service is running (`ollama serve`) and reachable at the URL in your `.env`, then use **Test** in the Models panel to verify.

**Dependency errors after an update.**
Remove `node_modules` and `package-lock.json`, then reinstall.

**The window will not close from the title bar.**
The overlay is frameless by design — quit with `Ctrl/Cmd + Q` or from the system tray menu.

---

## Intended Use

The assistant is built for situations where you need help with what is in front of you without leaving what you are doing: reviewing documents, debugging error output, taking notes during meetings, preparing for calls, learning unfamiliar material, or drafting responses with the relevant context already on screen.

It is intended for legitimate, consensual use. Do not use it in examinations, assessments, or any setting where outside assistance is prohibited, and be aware that recording other people may require their consent depending on your jurisdiction.

---

## License

ISC License. Free for personal and commercial use.
