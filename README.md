# Gemini CLI + ZeroWarp

Local-only ZeroWarp notification extension for [Gemini CLI](https://github.com/google-gemini/gemini-cli), maintained by Enigma Labs.

This fork preserves the useful terminal integration from the upstream Warp extension while removing Warp-owned distribution identity. It emits local OSC 777 escape sequences for ZeroWarp to parse; it does not include telemetry, analytics SDKs, or Warp cloud service calls.

## Features

### Native Notifications

Get native ZeroWarp notifications when Gemini CLI:
- Completes a task, with a concise prompt/response summary
- Needs your input after becoming idle
- Requests permission to run a tool

### Session Status

The extension keeps ZeroWarp informed of Gemini's local session state by emitting structured events:
- `prompt_submit` when you send a prompt
- `tool_complete` when a tool call finishes
- `permission_request` when Gemini needs approval
- `stop` when Gemini finishes a turn

These events stay in the local terminal session and are not sent to Warp cloud services.

## Installation

```bash
gemini extensions install https://github.com/kernelalex/zerowarp-geminicli-plugin.git
```

For local development:

```bash
gemini extensions link ~/zerowarp-geminicli-plugin
```

After installing, restart Gemini CLI for the extension to activate.

## Requirements

- ZeroWarp terminal
- [Gemini CLI](https://github.com/google-gemini/gemini-cli) v0.26.0+
- `jq` for JSON parsing

## How It Works

Each hook script builds a structured JSON payload with `build-payload.sh` and writes an OSC 777 escape sequence to `/dev/tty`. ZeroWarp consumes that local terminal event to drive notifications and session UI.

Payloads include a negotiated protocol version, session ID, working directory, and event-specific fields. The protocol string remains `warp://cli-agent` for compatibility with the current ZeroWarp client.

The extension registers five hooks:
- `SessionStart` emits the extension version on startup.
- `AfterAgent` sends a task-complete notification with prompt/response context.
- `Notification` handles idle and tool-permission notifications.
- `BeforeAgent` signals that a submitted prompt is running.
- `AfterTool` signals that a tool call completed.

## Uninstall

```bash
gemini extensions uninstall zerowarp-geminicli-plugin
```

## Versioning

The extension version in `gemini-extension.json` is checked by the ZeroWarp client to detect outdated installations. When bumping this version, also update the matching minimum plugin version in ZeroWarp.

## Attribution

This project is a privacy-focused fork of the Warp Gemini CLI notification extension. ZeroWarp-specific changes are maintained by Enigma Labs.

## License

MIT License. See [LICENSE](LICENSE) for details.
