# ◆ ruth

Installer and releases for Ruth, a local-first all-purpose AI agent that runs entirely on your machine, with no cloud and no telemetry.

## Install

On linux-x64 (including Chromebook Linux):

```bash
curl -L https://the3kingleo-cmyk.github.io/ruth-install | sh
```

No GitHub token is required. The script:

- Installs `ruth` to `~/.local/bin/ruth`, plus `Ruth` and `RUTH` links, and adds `~/.local/bin` to `PATH` in `~/.bashrc` and `~/.profile`
- Writes a default config in `~/.config/ruth/` if you do not already have one, pointing at the local Ollama model `ruth`
- Installs Ollama if it is missing and can be installed without a password prompt, then builds the local uncensored `ruth` model

After install, open a new terminal and type `ruth`.

## Requirements

- Linux x64
- Enough memory to run the default local `ruth` model in Ollama (the model files are about 1.1 GB)

Ollama auto-install needs root or passwordless `sudo`. If that is not available, install Ollama yourself, then re-run this installer.

## License

MIT. Ruth is built on an MIT-licensed OpenCode foundation.
