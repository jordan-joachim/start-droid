# start-droid

A small interactive launcher for the [Factory Droid CLI](https://docs.factory.ai/cli/getting-started/overview) that switches between Ollama and OpenRouter model backends and lets you pick a recent session from a terminal UI.

## What it does

- Starts Droid with a backend-specific runtime settings file:
  - `ollama` (default) — local/cloud Ollama models via `http://127.0.0.1:11434/v1`
  - `openrouter` — [OpenRouter](https://openrouter.ai) cloud models
- `refresh` — updates model limits from each provider's API, capping OpenRouter output tokens to a safe chat-completions value of `16384`, then launches with the Ollama file
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
- For `openrouter` mode: an `OPENROUTER_API_KEY` environment variable

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
cp templates/openrouter.settings.json.example ~/.factory/start-droid/openrouter.settings.json
```

Set your OpenRouter API key when using openrouter mode:

```bash
export OPENROUTER_API_KEY="your-openrouter-api-key"
```

## Usage

```bash
start-droid -m ollama      # default; use local Ollama models
start-droid -m openrouter  # use OpenRouter cloud models
start-droid -m refresh     # refresh model context lengths, then pick session
```

## How refresh works

- **Ollama**: queries `POST /api/show` for each known model and reads the `<model>.context_length` value from `model_info`.
- **OpenRouter**: queries `GET /api/v1/models`, reads `context_length` for each mapped model, and caps `maxOutputTokens` at `16384`. Uses the OpenRouter model ID (e.g. `nvidia/nemotron-3-ultra-550b-a55b`) as the lookup key.

The script overwrites `~/.factory/start-droid/ollama.settings.json` and `~/.factory/start-droid/openrouter.settings.json` in place.

## Files

- `start-droid` — the launcher script
- `templates/ollama.settings.json.example` — example Ollama backend settings
- `templates/openrouter.settings.json.example` — example OpenRouter backend settings

## Notes

- No API keys or credentials are committed to this repository.
- The OpenRouter template uses `${OPENROUTER_API_KEY}` so the key is read from the environment at runtime.
- Delete only removes session `.jsonl` and `.settings.json` files from `~/.factory/sessions`; it leaves other data untouched.
