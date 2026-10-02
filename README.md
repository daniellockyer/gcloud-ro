# gcloud-ro

A read-only allowlist wrapper around `gcloud`. Use it in an agent's command allowlist instead of `gcloud` so only read-only commands (`list`, `describe`, `get`, `read`, etc.) can run. Unknown verbs are denied.

## Install

Download [`bin/gcloud-ro`](https://raw.githubusercontent.com/daniellockyer/gcloud-ro/main/bin/gcloud-ro) from GitHub:

```bash
mkdir -p ~/.local/bin
curl -fsSL https://raw.githubusercontent.com/daniellockyer/gcloud-ro/main/bin/gcloud-ro \
  -o ~/.local/bin/gcloud-ro
chmod +x ~/.local/bin/gcloud-ro
```

Or from a local clone:

```bash
mkdir -p ~/.local/bin
cp bin/gcloud-ro ~/.local/bin/
chmod +x ~/.local/bin/gcloud-ro
```

Make sure `~/.local/bin` is on your `PATH`.

## Usage

```bash
bin/gcloud-ro compute instances list
bin/gcloud-ro --print-policy   # show the allow/deny policy
bin/gcloud-ro --self-test      # run built-in tests
```

## Environment

- `GCLOUD_RO_BIN` — real `gcloud` binary (default: first `gcloud` on `PATH`)
- `GCLOUD_RO_DRY_RUN=1` — print the approved command and exit without running it
