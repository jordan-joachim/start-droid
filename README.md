# start-droid

A small interactive launcher for the [Factory Droid CLI](https://docs.factory.ai/cli/getting-started/overview) that switches between Ollama and Kilo model backends and lets you pick a recent session from a terminal UI.

## What it does

- Starts Droid with a backend-specific runtime settings file:
  - `ollama` (default) — local/cloud Ollama models via `http://127.0.0.1:11434/v1`
  - `kilo` — [Kilo AI Gateway](https://kilo.ai/docs/gateway/api-reference) cloud models
  - `refresh` — updates `maxOutputTokens` from each provider's API, then launches with the Ollama file
- Presents an interactive session picker:
  - `[NEW]` is always at the top
  - Lists the 10 most recent sessions by default
  - Scroll with `↑`/`↓` (or `k`/`j`)
  - Window scrolls down after item 5 to load up to 10 older sessions
  - `Enter` resumes the selected session (or starts a new one)
  - `Delete`/`Backspace`/`d` removes the selected session
- Requires a TTY; it exits cleanly if run non-interactively.

## Requirements

- Python 3 (no third-party packages)
- Droid CLI installed and on `$PATH`
- For `ollama` mode: a running Ollama server at `127.0.0.1:11434`
- For `kilo` mode: a `KILO_API_KEY` environment variable

## Install

Copy the script to a directory on your `$PATH`, for example:

```bash
cp start-droid ~/.local/bin/start-droid
chmod +x ~/.local/bin/start-droid
```

Create the runtime settings directory and copy the templates:

```bash
mkdir -p ~/.factory/start-droid
cp templates/ollama.settings.json.example ~/.factory/start-droid/ollama.settings.json
cp templates/kilo.settings.json.example ~/.factory/start-droid/kilo.settings.json
```

Set your Kilo API key when using kilo mode:

```bash
export KILO_API_KEY="your-kilo-api-key"
```

## Usage

```bash
start-droid -m ollama    # default; use local Ollama models
start-droid -m kilo      # use Kilo AI cloud models
start-droid -m refresh   # refresh model context lengths, then pick session
```

## How refresh works

- **Ollama**: queries `POST /api/show` for each known model and reads the `<model>.context_length` value from `model_info`.
- **Kilo**: queries `GET /api/gateway/models` and reads `context_length` for each mapped model.

The script overwrites `~/.factory/start-droid/ollama.settings.json` and `~/.factory/start-droid/kilo.settings.json` in place.

## Files

- `start-droid` — the launcher script
- `templates/ollama.settings.json.example` — example Ollama backend settings
- `templates/kilo.settings.json.example` — example Kilo backend settings

## Notes

- No API keys or credentials are committed to this repository.
- The Kilo template uses `${KILO_API_KEY}` so the key is read from the environment at runtime.
- Delete only removes session `.jsonl` and `.settings.json` files from `~/.factory/sessions`; it leaves other data untouched.
