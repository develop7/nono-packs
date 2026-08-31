# nono jcode

Sandbox profile for running [J-Code](https://jcode.sh) — a TUI coding agent that talks to Claude Max / ChatGPT Pro subscriptions and local providers — inside a [nono](https://nono.sh) security sandbox.

Install:

```
nono run --profile jcode -- jcode
```

If the pack isn't already installed, nono will prompt to pull it.

## What's in the pack

- **`policy.json`** — sandbox profile (loaded as `--profile jcode`). Grants jcode's state directories, provider network access, and the standard runtime groups.

## What the profile grants

jcode is a coding agent: it edits files in the working directory, spawns build/test tooling, and talks to LLM providers. The profile mirrors the other agent packs in this registry:

- **Filesystem** — read+write on jcode's own state: `~/.jcode` (config, auth, sessions, memory, logs), `~/.config/jcode` (per-provider credential files), `~/.cache/jcode` (latex/mermaid/scratch rendering), `~/.local/share/jcode`, `~/.local/state/jcode`. Read-only on `$NONO_PACKAGES` and `$NONO_CONFIG/profiles`.
- **Workdir** — read+write, so jcode can edit the project it is pointed at.
- **Network** — outbound allowed (default), with phantom-credential routes for OpenAI, Anthropic, Gemini, GitHub, and GitLab. Add a route to the `credentials` array in a child profile to activate it, or use `custom_credentials` for other providers (OpenRouter, local Ollama, etc.).
- **Runtimes** — Node, Rust, Python, Nix, plus git config read and Linux sysfs/cache access, matching the other agent packs.
- **Open URLs** — `auth.openai.com`, `claude.ai`, `github.com`, and localhost (OAuth callbacks).
- **Daemon socket** — connect+bind on Unix sockets under `$XDG_RUNTIME_DIR`, so `jcode serve` can bind its default daemon socket.

## Daemon mode

`jcode serve` runs a background daemon that binds a Unix socket at `$XDG_RUNTIME_DIR/jcode.sock` by default. The profile grants connect+bind on sockets under `$XDG_RUNTIME_DIR`, so the daemon works out of the box. To keep the socket inside jcode's own state dir instead, point it there explicitly:

```
jcode serve --socket "$HOME/.jcode/jcode.sock"
```

## Custom profiles

Create a custom profile to add credential routes, extra filesystem grants, or other customizations:

```bash
nono profile init jcode --extends nolabs-ai/jcode --full
```

## Source

`https://github.com/nolabs-ai/nono-packs/tree/main/jcode`

Published via Sigstore-signed releases triggered by tags matching `jcode-v*`.
