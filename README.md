# Context-Aware AI Desktop Assistant

*A privacy-first, fully offline reimagining of "invisible AI overlay" tools (e.g. Cluely), built as a modular software engineering project.*

## 1. Overview

It is a context-aware AI desktop assistant that watches what's on the user's screen and gives instant, relevant help — explanations, summaries, code suggestions, answers — without the user ever copy-pasting anything into a chatbot.

When the user presses a global shortcut, It captures the active window or a selected region, runs Optical Character Recognition (OCR) to extract the visible text, sends that context to a locally hosted LLM (served via Ollama), and displays the AI's response in a lightweight overlay window. Everything — capture, OCR, inference, and storage — runs on the user's machine. No screen content, keystrokes, or personal data ever leaves the device.

Unlike commercial products in this space that stream audio/video to cloud APIs, It is designed from the ground up as an **offline-first, local-inference system**, making it a suitable academic demonstration of desktop app architecture, systems integration, and applied AI — without any data-privacy or cloud-cost concerns.

## 2. Problem It Solves

Modern knowledge workers and students constantly switch context — between an IDE and Stack Overflow, a PDF and a chatbot, a spreadsheet and documentation. Getting AI help today means manually describing or copy-pasting what's on screen into a separate chat window, which:

- Breaks flow and wastes time on repetitive copy-paste.
- Loses visual/layout context that plain text can't capture (e.g. error messages, diagrams, tables).
- Forces sensitive on-screen data (code, financial info, personal documents) through third-party cloud APIs.

It removes the copy-paste step entirely and keeps all processing local, so users get instant contextual help without breaking their workflow or trusting their screen contents to an external server.

## 3. Target Users (Personas)

**Sara, the CS Student**
Debugging code in her IDE, reading dense lecture PDFs, and constantly alt-tabbing to ChatGPT to ask "what does this error mean?" Wants instant explanations without breaking flow and without paying for API credits.

**Dev, the Software Engineer**
Reviews unfamiliar codebases and technical documentation daily. Wants quick, contextual code explanations and doesn't want proprietary company code touching a third-party cloud LLM.

**Priya, the Privacy-Conscious Professional**
Works with sensitive client documents and financial data. Wants AI assistance but is barred (by policy or preference) from using cloud AI tools on her screen content.

**Alex, the Lifelong Learner**
Reads research papers and long-form articles and wants on-demand summaries and simplified explanations without switching apps or subscriptions.

## 4. Vision Statement

To build a privacy-respecting, always-available desktop assistant that understands whatever a user is looking at — instantly, offline, and without ever sending their screen to the cloud — demonstrating that useful, real-time AI assistance and user privacy are not mutually exclusive.

## 5. Key Features / Goals

- **Global hotkey invocation** — summon the assistant from any application with one keystroke.
- **Automatic screen/window/region capture** — no manual screenshotting required.
- **OCR-based context extraction** — pulls readable text from the captured image.
- **Local LLM inference** — runs entirely through a locally hosted model (e.g., via Ollama), no external API calls.
- **Overlay response UI** — a lightweight, always-on-top window shows AI output without stealing focus from the user's task.
- **Conversation history & follow-ups** — maintains session context so users can ask follow-up questions naturally.
- **Configurable capture modes** — active window, custom region, or full screen.
- **Modular, layered architecture** — independent components for capture, OCR, context management, inference, and UI, enabling future plugin support, voice input, document retrieval (RAG), and IDE integrations.
- **Robust local data handling** — conversation history and settings persisted locally with no cloud dependency.

## 6. Success Metrics

| Metric | Target |
|---|---|
| End-to-end response latency (capture → AI response displayed) | Under 5 seconds on a mid-range consumer laptop (CPU-only inference) |
| OCR extraction accuracy on standard text (code, articles, UI text) | ≥ 90% character accuracy on clean screen captures |
| Network calls during normal operation | Zero (verifiable via traffic monitoring) |
| Application types supported at demo | At least 3 (IDE, browser/PDF reader, plain text/terminal) |
| Crash-free session rate during grading demo | 100% across at least 10 consecutive invocations |
| Module test coverage (capture, OCR, context manager) | ≥ 70% unit test coverage per module |
| Follow-up question contextual accuracy | Correctly retains prior turn context in ≥ 90% of manual test cases |

## 7. Assumptions & Constraints

**Assumptions**

- The user's machine has sufficient CPU/RAM (or GPU) to run a small-to-medium quantized local LLM (e.g., a 7B-parameter model) at usable speed.
- Screen content targeted for assistance is primarily text-based (code, articles, UI text, documents) rather than dense imagery or video.
- The user grants necessary OS-level permissions for screen capture and global hotkey registration (e.g., macOS Screen Recording / Accessibility permissions).
- Ollama (or an equivalent local model runtime) is installed and at least one model is pulled before first use.

**Constraints**

- Local LLM output quality will generally be lower than frontier cloud models, given hardware limits on model size.
- OCR accuracy degrades on stylized fonts, low-contrast UI, dense diagrams, or non-Latin scripts not covered by the OCR engine's trained languages.
- Global hotkey capture and screen recording APIs differ across Windows, macOS, and Linux, requiring platform-specific handling.
- This is an academic project: no cloud sync, multi-user accounts, enterprise auth, or compliance certifications are in scope.
- Voice interaction, plugin marketplace, and document-retrieval (RAG) are documented as future enhancements, not part of the initial deliverable.

## 8. High-Level Architecture Summary

It follows a modular, layered architecture:

1. **Frontend (Presentation Layer):** Overlay UI and settings/history panel, built with an Electron/Tauri-based desktop shell.
2. **Backend (Application/Service Layer):** Screen capture module, OCR engine, context manager, and an async orchestrator that coordinates requests between components via IPC.
3. **AI Inference Layer:** A prompt builder and local LLM runtime (Ollama), optionally containerized for reproducible deployment.
4. **Data Layer:** Local SQLite database for conversation history, a config store for user settings, and a local cache/embedding store for fast context recall.
5. **Deployment/Infrastructure:** Packaged desktop installers (electron-builder/Tauri bundler) per OS, an optional Docker container for the local LLM runtime, and an auto-update mechanism via GitHub Releases.

** See `architecture-diagram.drawio` (open in [draw.io](https://app.diagrams.net)) for the full visual architecture, including data flow arrows and a legend. **
