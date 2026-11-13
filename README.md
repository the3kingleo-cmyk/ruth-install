# Ruth installer

Public, token-free installer and release assets for **Ruth**, a personal MIT-licensed fork of the archived [OpenCode](https://github.com/opencode-ai/opencode) Go / Bubble Tea TUI. The original copyright is retained; a 2026 copyright line for David Anderson is added.

Install on Chromebook Linux (or other linux-x64) with:

```bash
curl -L https://the3kingleo-cmyk.github.io/ruth-install | sh
```

No GitHub token is required. The script:

- Installs `ruth` to `~/.local/bin/ruth`, plus `Ruth`, `RUTH`, and `opencode` symlinks to the same binary, and adds `~/.local/bin` to `PATH` in both `~/.bashrc` and `~/.profile`
- Writes a default `~/.ruth.json` (and `~/.config/ruth/.ruth.json`) if you do not already have one, pointing at the local Ollama model `ruth`
- Sets `LOCAL_ENDPOINT=http://localhost:11434/v1`
- Starts Ollama (systemd or `ollama serve` in the background) and waits until it is ready before creating the `ruth` model
- Removes leftover TypeScript Ruth files (`~/.local/share/ruth/plugin`, the old `ruth.json` format) and moves `~/.opencode` aside
- Installs `zstd` and Ollama if they are missing and can be installed without a password prompt, then builds the local model `ruth` from Qwen3 1.7B abliterated (Apache 2.0)

After install, close the terminal, open a new one, and type `Ruth` (or `ruth`, `RUTH`, or `opencode`) to open the full Ruth interface. No provider API key is required.

## License

The `ruth` binary is MIT-licensed. See the source repository [the3kingleo-cmyk/ruth-go](https://github.com/the3kingleo-cmyk/ruth-go) `LICENSE`.

## Limits

- The installer only targets linux-x64 (including Chromebook Linux).
- Ollama auto-install needs root or passwordless `sudo`. If that is not available, install `zstd` and Ollama yourself, then re-run this installer.
