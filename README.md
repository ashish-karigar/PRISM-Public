# Prism

Prism is a desktop job-application workflow app that turns a pasted job description into a structured Notion entry and, optionally, a tailored cover letter PDF. It runs local AI through Ollama, so job descriptions and cover-letter generation stay on the user's machine.

Developed by **@ashish.karigar**

---

## Features

- Paste a job description and extract structured fields using local AI
- Save extracted job application details directly to a Notion database
- Optionally generate a tailored cover letter PDF
- Save cover letters automatically to the user's Downloads folder
- Configure Notion token and database/data source ID from the Settings screen
- Customize cover-letter instructions and personal knowledge base
- First-run setup screen for:
  - User name
  - Ollama installation
  - Ollama startup
  - `qwen3:8b` model download
- Real-time model download progress bar with downloaded size
- macOS DMG build support through Tauri

---

## Tech Stack

### Frontend
- React
- TypeScript
- Vite
- CSS
- Lucide React icons

### Desktop Runtime
- Tauri
- Rust

### AI Runtime
- Ollama
- `qwen3:8b`

### Integrations
- Notion API
- Local PDF generation using Rust `printpdf`

---

## How Prism Works

1. User launches Prism.
2. Prism checks whether the user's name is saved.
3. Prism checks whether Ollama is installed, running, and has the required model.
4. If needed, Prism asks permission to install/start Ollama and download the model.
5. Once local AI is ready, the main app UI opens.
6. User pastes a job description.
7. Prism extracts structured job fields using local Ollama.
8. Prism saves the job application to Notion.
9. If selected, Prism generates a cover letter PDF and saves it to Downloads.

---

## Required Notion Database Fields

Your Notion database/data source should include these fields:

| Field Name | Type |
|---|---|
| Company Name | Title |
| Role / Position | Text |
| Application Status | Status |
| Location | Text |
| Application Link / Portal | URL |
| Job ID | Text |
| Work Mode | Select |
| Type | Select |
| Salary Expectation | Number |
| Contact Person | Text |
| Email / Phone | Text |
| Deadline | Date |
| Notes | Text |
| Salary Range | Text |

### Expected Select / Status Options

Application Status:

- Applied
- not applied yet
- interview scheduled
- in progress
- offer recieved
- rejected

Work Mode:

- unsure
- hybrid
- offline
- onsite

Type:

- unsure
- internship
- full time

---

## Ollama Setup Behavior

Prism checks for Ollama on startup.

If Ollama is missing, Prism asks the user for permission and attempts to install it.

If Ollama is installed but not running, Prism starts it.

If the required model is missing, Prism downloads:

```bash
qwen3:8b
```

The model download screen shows:

- Real-time percentage
- Downloaded size
- Total model size
- Progress bar

---

## Manual Ollama Commands

Useful commands for testing:

### Check if Ollama is installed

```bash
which ollama
```

### Check if Ollama server is running

```bash
curl http://localhost:11434/api/tags
```

### Start Ollama manually

```bash
ollama serve
```

### List installed models

```bash
ollama list
```

### Pull required model manually

```bash
ollama pull qwen3:8b
```

---

## Fully Remove Ollama for First-Run Testing on macOS

Use this only when testing Prism's first-run setup.

```bash
pkill -f ollama
```

```bash
sudo rm -rf /Applications/Ollama.app
sudo rm -f /usr/local/bin/ollama
sudo rm -f /opt/homebrew/bin/ollama
```

```bash
rm -rf "$HOME/Library/Application Support/Ollama"
rm -rf "$HOME/Library/Saved Application State/com.electron.ollama.savedState"
rm -rf "$HOME/Library/Caches/com.electron.ollama"
rm -rf "$HOME/Library/Caches/ollama"
rm -rf "$HOME/Library/WebKit/com.electron.ollama"
rm -rf "$HOME/.ollama"
```

Verify removal:

```bash
which ollama
```

Expected:

```bash
ollama not found
```

Then verify the local server is stopped:

```bash
curl http://localhost:11434/api/tags
```

Expected: connection failure.

---

## Settings Storage

Prism saves user settings locally in the user's config directory under:

```bash
Prism/settings.json
```

Saved settings include:

- User name
- Notion token
- Notion database/data source ID
- Cover letter prompt
- Knowledge base summary

---

## Cover Letter Behavior

If the user provides a custom cover-letter prompt, Prism uses it.

If not, Prism falls back to the default prompt and uses the saved user name.

If no user name exists, Prism falls back to:

```bash
username
```

Generated PDFs are saved to the user's Downloads folder with a filename based on company name and role.

---

## Development Notes

### Important Tauri Commands

Rust commands currently used by the frontend include:

- `check_ollama_ready`
- `install_ollama`
- `start_ollama`
- `pull_ollama_model`
- `stop_ollama`
- `save_notion_settings`
- `load_notion_settings`
- `test_notion_connection`
- `extract_job_fields`
- `save_to_notion`
- `generate_cover_letter`
- `save_cover_letter_to_downloads`

### Startup Flow

The frontend startup states include:

- `checking`
- `needs_name`
- `needs_permission`
- `installing`
- `starting`
- `pulling_model`
- `ready`
- `error`

The main Prism UI is hidden until startup status is `ready`.

---

## Known Notes

- Ollama installation may require system permissions depending on the user's machine.
- On macOS, users may need to right-click and choose **Open** if Gatekeeper blocks the app.
- For public distribution, the app should eventually be code-signed and notarized with an Apple Developer account.
- Model download time depends on internet speed and machine performance.

---

## Future Improvements

- Add auto-update support
- Add a Notion schema validation screen
- Add multiple AI model options
- Add Windows installer testing
- Add signed and notarized macOS release
- Add export/import settings
- Add job application history inside Prism
- Add retry/resume behavior for interrupted model downloads


## License

This project is licensed under the MIT License.
