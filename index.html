#!/bin/sh
# Install Ruth and the Chromebook-lite Oh My OpenCode plugin from the public
# the3kingleo-cmyk/ruth-install GitHub releases. No GitHub token is required.
# Writes only under $HOME (except an optional Ollama system install).
# Do not run this on a machine the user did not ask to modify.
set -eu

INSTALL_REPO="${RUTH_INSTALL_REPO:-the3kingleo-cmyk/ruth-install}"
RELEASE_BASE="${RUTH_RELEASE_BASE:-https://github.com/${INSTALL_REPO}/releases/latest/download}"
RUTH_ASSET="${RUTH_ASSET:-ruth-linux-x64.tar.gz}"
PLUGIN_ASSET="${PLUGIN_ASSET:-oh-my-openagent-chromebook-lite.tar.gz}"
RUTH_BIN_DIR="${RUTH_BIN_DIR:-$HOME/.local/bin}"
OMO_INSTALL_DIR="${OMO_INSTALL_DIR:-$HOME/.local/share/ruth/plugin}"
CONFIG_DIR="${RUTH_CONFIG_DIR:-$HOME/.config/ruth}"
INSTALL_PLUGIN="${INSTALL_PLUGIN:-1}"
INSTALL_OLLAMA="${INSTALL_OLLAMA:-1}"
CHAT_MODEL="${RUTH_CHAT_MODEL:-ruth}"
TOOLS_MODEL="${RUTH_TOOLS_MODEL:-ruth-pro}"
BASE_CHAT="${RUTH_BASE_CHAT_MODEL:-huihui_ai/qwen3-abliterated:1.7b}"
BASE_TOOLS="${RUTH_BASE_TOOLS_MODEL:-huihui_ai/qwen3-abliterated:4b}"

log() { printf '%s\n' "$*"; }
die() { printf 'error: %s\n' "$*" >&2; exit 1; }

command -v curl >/dev/null 2>&1 || die "curl is required"
command -v tar >/dev/null 2>&1 || die "tar is required"

tmpdir="$(mktemp -d /tmp/ruth-install.XXXXXX)"
trap 'rm -rf "$tmpdir"' EXIT

download() {
  local url="$1" dest="$2"
  log "Downloading $url ..."
  curl -fL --retry 3 --retry-delay 2 -o "$dest" "$url" \
    || die "Failed to download $url"
}

verify_sha256() {
  local file="$1" sumfile="$2"
  if [ ! -f "$sumfile" ]; then
    die "Missing sha256 file for $(basename "$file")"
  fi
  local expected actual
  expected="$(tr 'A-F' 'a-f' < "$sumfile" | grep -oE '[0-9a-f]{64}' | head -n 1)"
  [ -n "$expected" ] || die "Could not parse sha256 from $sumfile"
  if command -v sha256sum >/dev/null 2>&1; then
    actual="$(sha256sum "$file" | awk '{print $1}')"
  elif command -v shasum >/dev/null 2>&1; then
    actual="$(shasum -a 256 "$file" | awk '{print $1}')"
  else
    die "sha256sum or shasum is required to verify the download"
  fi
  [ "$actual" = "$expected" ] || die "sha256 mismatch for $(basename "$file"): expected $expected, got $actual"
  log "Verified sha256 for $(basename "$file")"
}

# --- Ruth binary ---
download "${RELEASE_BASE}/${RUTH_ASSET}" "$tmpdir/$RUTH_ASSET"
download "${RELEASE_BASE}/${RUTH_ASSET}.sha256" "$tmpdir/${RUTH_ASSET}.sha256"
verify_sha256 "$tmpdir/$RUTH_ASSET" "$tmpdir/${RUTH_ASSET}.sha256"

mkdir -p "$tmpdir/ruth-unpack"
tar -xzf "$tmpdir/$RUTH_ASSET" -C "$tmpdir/ruth-unpack"
bin="$(find "$tmpdir/ruth-unpack" -type f \( -name ruth -o -name jcode -o -name opencode \) | head -n 1)"
[ -n "$bin" ] || die "Downloaded $RUTH_ASSET did not contain a ruth binary"
mkdir -p "$RUTH_BIN_DIR"
install -m 0755 "$bin" "$RUTH_BIN_DIR/ruth"
# `opencode` must launch this same Ruth TUI with nothing else showing.
ln -sfn ruth "$RUTH_BIN_DIR/opencode"
log "Installed $RUTH_BIN_DIR/ruth and $RUTH_BIN_DIR/opencode (same build)"

# --- Default config (do not overwrite an existing one) ---
mkdir -p "$CONFIG_DIR"
if [ ! -f "$CONFIG_DIR/ruth.json" ] && [ ! -f "$CONFIG_DIR/ruth.jsonc" ]; then
  cat > "$CONFIG_DIR/ruth.json" << PRESET
{
  "\$comment": "Default Ruth config. Local model is named Ruth (Ollama \`ruth\` on Qwen3 1.7B, Apache 2.0). Use \`ruth-pro\` (4B) for heavier tool work.",
  "model": "ollama/ruth",
  "small_model": "ollama/ruth",
  "websearch": { "provider": "random" },
  "permissions": [
    { "action": "execute", "resource": "*", "effect": "deny" }
  ],
  "plugins": [],
  "provider": {
    "ollama": {
      "options": {
        "headerTimeout": 1200000,
        "chunkTimeout": false
      },
      "models": {
        "ruth": {
          "capabilities": { "tools": true }
        },
        "ruth-pro": {
          "capabilities": { "tools": true }
        },
        "${BASE_TOOLS}": {
          "capabilities": { "tools": true }
        },
        "${BASE_CHAT}": {
          "capabilities": { "tools": true }
        }
      }
    }
  }
}
PRESET
  log "Wrote default $CONFIG_DIR/ruth.json (model: ollama/ruth; tools: ollama/ruth-pro)"
else
  log "Keeping existing Ruth config in $CONFIG_DIR"
fi

# --- Plugin bundle ---
install_plugin="0"
case "${INSTALL_PLUGIN}" in
  1|true|yes|YES|TRUE) install_plugin="1" ;;
esac

if [ "$install_plugin" = "1" ]; then
  download "${RELEASE_BASE}/${PLUGIN_ASSET}" "$tmpdir/$PLUGIN_ASSET"
  download "${RELEASE_BASE}/${PLUGIN_ASSET}.sha256" "$tmpdir/${PLUGIN_ASSET}.sha256"
  verify_sha256 "$tmpdir/$PLUGIN_ASSET" "$tmpdir/${PLUGIN_ASSET}.sha256"
  mkdir -p "$tmpdir/plugin-unpack" "$(dirname "$OMO_INSTALL_DIR")"
  tar -xzf "$tmpdir/$PLUGIN_ASSET" -C "$tmpdir/plugin-unpack"
  # Accept either a top-level folder or files at the archive root.
  src="$tmpdir/plugin-unpack"
  if [ ! -f "$src/dist/index.js" ]; then
    idx="$(find "$tmpdir/plugin-unpack" -type f -name index.js | grep '/dist/index.js$' | head -n 1 || true)"
    if [ -n "${idx:-}" ]; then
      src="$(CDPATH= cd -- "$(dirname -- "$idx")/.." && pwd)"
    fi
  fi
  [ -n "${src:-}" ] && [ -f "$src/dist/index.js" ] || die "Plugin archive did not contain dist/index.js"
  rm -rf "$OMO_INSTALL_DIR"
  mkdir -p "$OMO_INSTALL_DIR"
  # copy including hidden files, but not the directory itself as a nested root
  cp -a "$src"/. "$OMO_INSTALL_DIR/"
  log "Installed plugin at $OMO_INSTALL_DIR"

  if command -v jq >/dev/null 2>&1; then
    cfg="$CONFIG_DIR/ruth.json"
    [ -f "$cfg" ] || cfg="$CONFIG_DIR/ruth.jsonc"
    if [ -f "$cfg" ]; then
      tmp="$(mktemp)"
      jq --arg p "$OMO_INSTALL_DIR" '
        .plugins = ((.plugins // [])
          | map(if type=="string" then . else .package end)
          | map(select(. != $p and . != ($p+"/") ))
          + [$p])
      ' "$cfg" > "$tmp" && mv "$tmp" "$cfg"
      log "Registered plugin $OMO_INSTALL_DIR in $cfg"
    fi
  else
    # jq is optional: write or refresh plugins with a small python/python3 helper if present.
    cfg="$CONFIG_DIR/ruth.json"
    if command -v python3 >/dev/null 2>&1 && [ -f "$cfg" ]; then
      python3 - "$cfg" "$OMO_INSTALL_DIR" << 'PY'
import json, sys
path, plugin = sys.argv[1], sys.argv[2]
with open(path, encoding="utf-8") as f:
    data = json.load(f)
plugins = data.get("plugins") or []
norm = []
for entry in plugins:
    value = entry if isinstance(entry, str) else (entry or {}).get("package")
    if value and value.rstrip("/") != plugin.rstrip("/"):
        norm.append(entry)
norm.append(plugin)
data["plugins"] = norm
with open(path, "w", encoding="utf-8") as f:
    json.dump(data, f, indent=2)
    f.write("\n")
PY
      log "Registered plugin $OMO_INSTALL_DIR in $cfg"
    else
      log "warning: jq/python3 not found; add \"$OMO_INSTALL_DIR\" to plugins in $CONFIG_DIR/ruth.json"
    fi
  fi
else
  log "Skipping plugin install (INSTALL_PLUGIN=0)"
fi

# --- PATH and env defaults ---
bashrc="${HOME}/.bashrc"
marker="# ruth chromebook-lite defaults"
if [ ! -f "$bashrc" ] || ! grep -qF "$marker" "$bashrc" 2>/dev/null; then
  if [ ! -f "$bashrc" ]; then
    : > "$bashrc"
  fi
  cat >> "$bashrc" << 'ENV'
# ruth chromebook-lite defaults
export RUTH_SSE_IDLE_TIMEOUT="${RUTH_SSE_IDLE_TIMEOUT:-600000}"
export RUTH_OLLAMA_ASSUME_TOOLS="${RUTH_OLLAMA_ASSUME_TOOLS:-1}"
case ":$PATH:" in
  *":$HOME/.local/bin:"*) ;;
  *) export PATH="$HOME/.local/bin:$PATH" ;;
esac
ENV
  log "Added Ruth PATH and env defaults to $bashrc"
fi
export PATH="$RUTH_BIN_DIR:$PATH"

# Keep this block last in ~/.bashrc so ~/.local/bin always wins over an official
# OpenCode install in ~/.opencode/bin.
shadow_official_opencode() {
  official="$HOME/.opencode/bin/opencode"
  if [ -e "$official" ] || [ -L "$official" ]; then
    dest="$HOME/.opencode/bin/opencode.official"
    n=1
    while [ -e "$dest" ]; do
      dest="$HOME/.opencode/bin/opencode.official.$n"
      n=$((n + 1))
    done
    mv "$official" "$dest"
    log "Moved official OpenCode aside to $dest so 'opencode' launches Ruth"
  fi
}
shadow_official_opencode

mkdir -p "$HOME/src"

# Ensure the Ruth PATH block is the last thing in bashrc (wins over later official installers
# only on this run; we also re-append a short tail so .local/bin is first in new shells).
tail_marker="# ruth: opencode is Ruth (keep last)"
if [ -f "$bashrc" ] && ! grep -qF "$tail_marker" "$bashrc"; then
  cat >> "$bashrc" << 'PATHTAIL'
# ruth: opencode is Ruth (keep last)
export PATH="$HOME/.local/bin:$PATH"
PATHTAIL
  log "Put ~/.local/bin first on PATH so 'opencode' and 'ruth' are this build"
fi

# --- Ollama ---
install_ollama="0"
case "${INSTALL_OLLAMA}" in
  1|true|yes|YES|TRUE) install_ollama="1" ;;
esac

ensure_ollama() {
  if command -v ollama >/dev/null 2>&1; then
    return 0
  fi
  [ "$install_ollama" = "1" ] || return 1
  log "Ollama is not on PATH. Installing from https://ollama.com/install.sh ..."
  if [ "$(id -u)" -eq 0 ]; then
    curl -fsSL https://ollama.com/install.sh | sh || return 1
  elif command -v sudo >/dev/null 2>&1 && sudo -n true 2>/dev/null; then
    curl -fsSL https://ollama.com/install.sh | sudo -n sh || return 1
  else
    log "Cannot auto-install Ollama (sudo would need a password). Install it yourself with:"
    log "  curl -fsSL https://ollama.com/install.sh | sh"
    return 1
  fi
  hash -r 2>/dev/null || true
  command -v ollama >/dev/null 2>&1
}


# Build named local models `ruth` (prefer 1.7b) and `ruth-pro` (4b).
# Never `ollama pull ruth` — that name is ours, not a registry image.
ollama_has() {
  ollama list 2>/dev/null | awk 'NR>1 {print $1}' | grep -qx "$1" && return 0
  ollama list 2>/dev/null | awk 'NR>1 {print $1}' | grep -qx "$1:latest" && return 0
  return 1
}

write_ruth_modelfile() {
  dest=$1
  from=$2
  cat > "$dest" << MODELFILE
FROM $from
PARAMETER temperature 0.2
SYSTEM """
You are Ruth, David's coding agent. Your name is Ruth. When asked your name or who you are, say you are Ruth.

Be brief and concrete. Do not show chain-of-thought.

When you need to create or overwrite a file, call the write tool.
- path is the filename only (for example hello.txt)
- content is the exact text to put inside the file, never the filename

Example:
User: write hello Ruth to hello.txt
You call write with path="hello.txt" and content="hello Ruth"
The file hello.txt then contains exactly: hello Ruth

Never put the filename in the file contents unless the user asked for that text. Do not add quotes, markdown, or extra lines the user did not ask for.

Tools: when you need a tool, make a real tool call. Do not write tool JSON or XML in your reply. If you cannot call a tool natively, emit a single JSON object of the form {"name":"<tool>","arguments":{...}} and nothing else.

Coding rules:
- Read a file before you edit it.
- Make the smallest correct change.
- After a tool result, keep going until the request is done.
- If a tool fails, change approach once; do not repeat the same call with the same arguments.
"""
MODELFILE
}

create_named_model() {
  name=$1
  from=$2
  mf="$tmpdir/Modelfile.$name"
  write_ruth_modelfile "$mf" "$from"
  log "Creating Ollama model $name from $from ..."
  if ollama create "$name" -f "$mf"; then
    log "Created $name"
    return 0
  fi
  log "warning: failed to create $name from $from"
  return 1
}

create_ruth_models() {
  ruth_from=""
  if ollama_has "$BASE_CHAT"; then
    log "Ollama already has $BASE_CHAT"
    ruth_from=$BASE_CHAT
  else
    log "Pulling $BASE_CHAT (base for the Ruth model, fits ~6.4GB RAM) ..."
    if ollama pull "$BASE_CHAT"; then
      ruth_from=$BASE_CHAT
    else
      log "warning: could not pull $BASE_CHAT; trying $BASE_TOOLS"
      if ollama_has "$BASE_TOOLS" || ollama pull "$BASE_TOOLS"; then
        ruth_from=$BASE_TOOLS
      else
        log "warning: could not pull a Qwen3 base model. Later run: ruth doctor --repair"
        return 1
      fi
    fi
  fi
  # Always (re)create `ruth` from this installer's Modelfile so identity
  # and the write-tool example stay current. Never `ollama pull ruth`.
  create_named_model ruth "$ruth_from" || true
  if ollama_has "$BASE_TOOLS" || ollama pull "$BASE_TOOLS"; then
    if ollama_has ruth-pro; then
      log "Ollama already has ruth-pro"
    else
      create_named_model ruth-pro "$BASE_TOOLS" || log "warning: ruth-pro (4b) was not created"
    fi
  else
    log "warning: skipped ruth-pro because $BASE_TOOLS could not be pulled"
  fi
}


if ensure_ollama; then
  if ! ollama list >/dev/null 2>&1; then
    log "Starting ollama serve in the background ..."
    nohup ollama serve >/tmp/ollama-serve.ruth-install.log 2>&1 &
    for _ in $(seq 1 30); do
      ollama list >/dev/null 2>&1 && break
      sleep 1
    done
  fi
  create_ruth_models || log "warning: could not build the Ruth Ollama models. Later run: ruth doctor --repair"
else
  log "Ollama is not available yet. After installing it, run: ruth doctor --repair"
fi

# Optional GitHub CLI for `ruth cloud` (Ollama in a Codespace).
install_gh() {
  if command -v gh >/dev/null 2>&1; then
    log "GitHub CLI (gh) is already on PATH"
    return 0
  fi
  case "${INSTALL_GH:-1}" in
    0|false|no|NO|FALSE)
      log "Skipping gh install (INSTALL_GH=0). For 'ruth cloud', run: sudo apt-get update && sudo apt-get install -y gh"
      return 0
      ;;
  esac
  if command -v apt-get >/dev/null 2>&1; then
    if [ "$(id -u)" -eq 0 ]; then
      apt-get update && apt-get install -y gh && { log "Installed GitHub CLI (gh)"; return 0; }
    elif command -v sudo >/dev/null 2>&1 && sudo -n true 2>/dev/null; then
      sudo -n apt-get update && sudo -n apt-get install -y gh && { log "Installed GitHub CLI (gh)"; return 0; }
    fi
  fi
  log "Could not auto-install the GitHub CLI. For 'ruth cloud', run: sudo apt-get update && sudo apt-get install -y gh"
}

install_gh

# Offer GitHub login at the end. Uses `gh auth login --web` (browser + device
# code; nothing to paste). Skippable and never blocks a piped installer (curl|sh).
# Set SKIP_GH_LOGIN=1 to suppress the offer entirely.
offer_gh_login() {
  case "${SKIP_GH_LOGIN:-0}" in
    1|true|yes|YES|TRUE)
      log "Skipped GitHub login (SKIP_GH_LOGIN=1). Later run: gh auth login --web"
      return 0
      ;;
  esac
  if ! command -v gh >/dev/null 2>&1; then
    log "After installing gh, connect GitHub with: gh auth login --web"
    log "That opens a browser and shows a short device code. Nothing to paste here. This step is optional."
    return 0
  fi
  if gh auth token >/dev/null 2>&1; then
    log "GitHub CLI is already logged in (Ruth will use: gh auth token)"
    return 0
  fi
  if [ -t 0 ] && [ -t 1 ]; then
    printf "Connect GitHub now? gh auth login --web opens a browser and shows a short device code (nothing to paste). Optional. [y/N] "
    # Default skip. A 20s timeout keeps a forgotten TTY from hanging the installer.
    ans=
    if command -v timeout >/dev/null 2>&1; then
      ans="$(timeout 20 sh -c 'read -r x && printf %s "$x"' || true)"
    else
      read -r ans || ans=
    fi
    case "$ans" in
      y|Y|yes|YES)
        gh auth login --web -h github.com -p https -s repo,codespace,read:org,workflow || log "gh auth login did not finish. Later run: gh auth login --web"
        ;;
      *)
        log "Skipped. Later run: gh auth login --web"
        ;;
    esac
  else
    log "To connect GitHub (optional, skippable; no token to paste): gh auth login --web"
    log "That opens a browser and shows a short device code. Ruth uses 'gh auth token' after that."
  fi
}
offer_gh_login

log ""
log "Next steps:"
log "  1. source ~/.bashrc"
log "  2. opencode --version    # or: ruth --version"
log "  3. opencode              # full TUI; or: opencode run 'hello'"
log "  4. (optional) gh auth login --web"
log "  5. (optional) ruth cloud up    # stop with: ruth cloud down"
log "Done."
