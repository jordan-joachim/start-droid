# start-droid

A small interactive launcher for the [Factory Droid CLI](https://docs.factory.ai/cli/getting-started/overview) that starts Droid with Ollama custom models and lets you pick a recent user session from a terminal UI.

## What it does

- Always uses the Ollama backend runtime settings file (`~/.factory/start-droid/ollama.settings.json`).
- Optional `--refresh` updates each configured model's `maxOutputTokens` from the running Ollama server via `POST /api/show`.
- Presents an interactive session picker:
  - `[NEW]` is always at the top
  - Lists the 10 most recent user sessions by default
  - Scroll with `↑`/`↓` (or `k`/`j`)
  - Window scrolls down after item 5 to load up to 10 older sessions
  - `Enter` resumes the selected session (or starts a new one)
  - `Delete`/`Backspace`/`d` removes the selected session
- Filters out subagent/worker sessions so only your own sessions are shown.
- Requires a TTY; exits cleanly if run non-interactively.

## Requirements

- Python 3 (no third-party packages)
- Droid CLI installed and on `$PATH`
- A running Ollama server at `127.0.0.1:11434` (override with `OLLAMA_BASE_URL`)

## Install

Copy the script to a directory on your `$PATH`:

```bash
cp start-droid ~/.local/bin/start-droid
chmod +x ~/.local/bin/start-droid
```

Create the runtime settings directory and copy the Ollama template:

```bash
mkdir -p ~/.factory/start-droid
cp templates/ollama.settings.json.example ~/.factory/start-droid/ollama.settings.json
```

## Usage

```bash
start-droid              # launch Droid with Ollama settings and pick session
start-droid --refresh    # refresh Ollama context lengths, then pick session
```

## How refresh works

For each configured Ollama model the script queries `POST /api/show` and reads the `<model>.context_length` value from `model_info`. The settings file is overwritten in place.

## Files

- `start-droid` — the launcher script
- `templates/ollama.settings.json.example` — example Ollama backend settings

## Notes

- No API keys or credentials are committed to this repository.
- Delete only removes session `.jsonl` and `.settings.json` files from `~/.factory/sessions`; it leaves other data untouched.
