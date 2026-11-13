# Ruth installer

Public, token-free installer and release assets for **Ruth**, a personal Chromebook-oriented coding agent.

Source code lives in private repositories. This repo only ships:

- `install.sh` — the one-line installer
- GitHub release assets: the `ruth` linux-x64 binary (plus sha256) and a prebuilt Oh My OpenCode chromebook-lite plugin bundle (plus sha256)

No GitHub token is required.

## Install

Type this (no token, no paste required):

```bash
curl -L the3kingleo-cmyk.github.io/ruth-install|sh
```

```bash
curl -fsSL https://raw.githubusercontent.com/the3kingleo-cmyk/ruth-install/main/install.sh | bash
```

The script:

- Downloads and sha256-verifies `ruth-linux-x64.tar.gz` from this repo's latest release
- Installs `ruth` to `~/.local/bin/ruth` and adds that directory to `PATH` in `~/.bashrc`
- Writes a default `~/.config/ruth/ruth.json` if you do not already have one
- Installs the prebuilt Chromebook-lite Oh My OpenCode plugin under `~/.local/share/oh-my-openagent` (set `INSTALL_PLUGIN=0` to skip)
- Installs Ollama if it is missing and can be installed without a password prompt, then pulls `huihui_ai/qwen3-abliterated:1.7b`

Then:

```bash
source ~/.bashrc
ruth run 'hello'
ruth doctor
```

`ruth upgrade` and `ruth self-update` also use these public releases. A GitHub token is optional.

## Plugin license

The Oh My OpenCode plugin is a modified copy of [code-yeongyu/oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent), redistributed free of charge for personal, non-commercial use under the **Sustainable Use License 1.0**. The bundle includes `LICENSE.md` and `NOTICE.md`. It is not affiliated with the original authors.

The `ruth` binary is MIT-licensed (see the private source repo's `LICENSE` and `NOTICE`).

## Limits

- This repo does not include source for Ruth or the plugin.
- The installer only targets linux-x64 (including Chromebook Linux).
- Ollama auto-install needs root or passwordless `sudo`. If that is not available, install Ollama from https://ollama.com and re-run, or just `ollama pull huihui_ai/qwen3-abliterated:1.7b`.


## Optional: `ruth cloud`

After install, `ruth cloud up` can run Ollama in a GitHub Codespace and tunnel it to `localhost:11435`. Local Ollama on `11434` stays as the automatic fallback. The installer tries to install the GitHub CLI (`gh`) when it can. A personal GitHub account has about 120 core-hours per month (about 30 hours on the 4-core machine). Stop the codespace with `ruth cloud down` when you are done.
