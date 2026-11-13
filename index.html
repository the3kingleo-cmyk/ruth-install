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
OMO_INSTALL_DIR="${OMO_INSTALL_DIR:-$HOME/.local/share/oh-my-openagent}"
CONFIG_DIR="${RUTH_CONFIG_DIR:-$HOME/.config/ruth}"
INSTALL_PLUGIN="${INSTALL_PLUGIN:-1}"
INSTALL_OLLAMA="${INSTALL_OLLAMA:-1}"
CHAT_MODEL="${RUTH_CHAT_MODEL:-huihui_ai/qwen3-abliterated:1.7b}"
TOOLS_MODEL="${RUTH_TOOLS_MODEL:-huihui_ai/qwen3-abliterated:4b}"

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
log "Installed $RUTH_BIN_DIR/ruth"

# --- Default config (do not overwrite an existing one) ---
mkdir -p "$CONFIG_DIR"
if [ ! -f "$CONFIG_DIR/ruth.json" ] && [ ! -f "$CONFIG_DIR/ruth.jsonc" ]; then
  cat > "$CONFIG_DIR/ruth.json" << PRESET
{
  "\$schema": "https://opencode.ai/config.json",
  "\$comment": "Default Ruth config for local Ollama. Prefer ${TOOLS_MODEL} when you need tools.",
  "model": "ollama/${CHAT_MODEL}",
  "small_model": "ollama/${CHAT_MODEL}",
  "websearch": { "provider": "random" },
  "plugins": [],
  "provider": {
    "ollama": {
      "options": {
        "headerTimeout": 1200000,
        "chunkTimeout": false
      },
      "models": {
        "${TOOLS_MODEL}": {
          "capabilities": { "tools": true }
        },
        "${CHAT_MODEL}": {
          "capabilities": { "tools": true }
        }
      }
    }
  }
}
PRESET
  log "Wrote default $CONFIG_DIR/ruth.json ($CHAT_MODEL chat; use $TOOLS_MODEL for tools)"
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

if ensure_ollama; then
  if ! ollama list >/dev/null 2>&1; then
    log "Starting ollama serve in the background ..."
    nohup ollama serve >/tmp/ollama-serve.ruth-install.log 2>&1 &
    for _ in $(seq 1 30); do
      ollama list >/dev/null 2>&1 && break
      sleep 1
    done
  fi
  if ollama list 2>/dev/null | grep -q "qwen3-abliterated:1.7b"; then
    log "Ollama already has $CHAT_MODEL"
  else
    log "Pulling the default chat model $CHAT_MODEL ..."
    ollama pull "$CHAT_MODEL" || log "warning: could not pull $CHAT_MODEL. Later run: ollama pull $CHAT_MODEL"
  fi
  if ! ollama list 2>/dev/null | grep -q "qwen3-abliterated:4b"; then
    log "For tool use, the 4b model is recommended. To install it: ollama pull $TOOLS_MODEL"
  fi
else
  log "Ollama is not available yet. After installing it, run: ollama pull $CHAT_MODEL"
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
  log "  Then: gh auth login   (needs the codespace scope, or a fine-grained token with Codespaces access on the3kingleo-cmyk/ruth-codespace)"
}

install_gh

log ""
log "Next steps:"
log "  1. source ~/.bashrc    (or open a new terminal so PATH includes ~/.local/bin)"
log "  2. ruth --version"
log "  3. ruth run 'hello'"
log "  4. ruth doctor         (repairs config/PATH/model if needed)"
log "  5. (optional) ruth cloud up    # Ollama in a GitHub Codespace; stop with ruth cloud down"
log "Done."
