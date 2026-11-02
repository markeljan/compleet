# Compleet

Compleet is an AI prompt compiler for developers. The primary CLI command is `tc`.

It can:

- turn rough text into terminal commands
- answer prompt-mode questions
- record a short voice note, transcribe it, and compile it into a structured AI prompt

## Install

Global install:

```bash
bun add -g compleet
tc --help
```

One-off usage:

```bash
bunx compleet --help
```

## Requirements

- `bun`
- `ffmpeg` for `tc voice`
- `zsh` or `bash` for shell integration

## Quick Start

Run setup once:

```bash
tc setup
```

`tc setup` now finishes provider onboarding first, then immediately checks voice mode readiness and offers the voice setup wizard if ffmpeg or a transcription backend still needs setup.

## Voice Mode

```bash
tc voice
```

Voice mode pipeline:

1. Record microphone audio.
2. Keep listening until you press Enter.
3. Transcribe speech locally when possible.
4. Fall back to the OpenAI speech API when needed.
5. Reuse Compleet's existing model path to turn the transcript into a structured prompt.

Example:

```text
Speech:
code optimize docker container and switch node to bun

Output:
Task: Optimize the Docker container for minimal size.

Requirements:
- Replace Node runtime with Bun if compatible.
- Remove unnecessary dependencies.
- Ensure the container builds successfully.

Output:
- Report the final image size.
```

### Local Transcription Backends

Compleet tries these backends in order:

1. `whisper.cpp` (recommended)
2. OpenAI speech-to-text API

When voice mode is not ready, the interactive setup wizard offers these options in order:

1. Install `ffmpeg` if recording is not available.
2. Set up `whisper.cpp` locally and download a default model.
3. Enter your own OpenAI API key fallback.

During recording, press Enter to stop.

If Compleet shell integration is installed, `tc voice` stages the compiled prompt into your next editable shell input instead of auto-running it.

Without shell integration, voice mode prints the compiled prompt to stdout.

Persistent settings live in:

```text
~/.config/compleet/config.json
```

Example voice section:

```json
{
  "voice": {
    "audioInputDevice": "Built-in Microphone",
    "whisperCppModelPath": "/Users/you/.config/compleet/voice/models/ggml-base.en.bin"
  }
}
```

Environment variables are still supported as temporary overrides for CI, debugging, or one-off runs, but the config file is the default place for durable settings.

## CLI Modes

| Mode | Example | Output |
| --- | --- | --- |
| Command generation | `tc list files in this directory` | Prints the generated command to stdout. |
| Explain (`-e`, `--explain`) | `tc -e find text files in this directory` | Prints the generated command to stdout, plus any available explanation and applicable risk information to stderr. It does not execute the command. |
| General prompt (`-p`, `--prompt`) | `tc -p explain when to use rsync vs scp` | Prints a general response to stdout without generating a command. |

If both `-p` and `-e` are supplied, general prompt mode takes precedence and `-e` has no effect.

## Config Commands

```bash
tc setup
tc setup codex
tc setup openai
tc config
tc config codex
tc config openai
tc reset
tc reset --yes
tc use codex
tc use openai
```

## Local Binary Build

Build the native binary for your current machine:

```bash
bun run build
./dist/tc --help
```

Create an export bundle with install scripts:

```bash
bun run export
ls dist/export
./dist/export/<bundle-name>/install.sh
```

Or install the JS project directly for local testing:

```bash
bun link
tc --help
```
