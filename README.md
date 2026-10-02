# gcloud-ro

A wrapper that only runs read-only `gcloud` commands (`list`, `describe`, `get`, `read`, …). Put it in your AI agent's command allowlist instead of `gcloud`. Anything it doesn't recognise is denied.

> [!WARNING]
> This is a client-side filter, not a security boundary. An agent that can run `gcloud`, `curl`, or a client library directly bypasses it. Always pair it with [read-only credentials](#read-only-credentials).

## Install

```bash
mkdir -p ~/.local/bin
curl -fsSL https://raw.githubusercontent.com/daniellockyer/gcloud-ro/main/bin/gcloud-ro -o ~/.local/bin/gcloud-ro
chmod +x ~/.local/bin/gcloud-ro
```

Make sure `~/.local/bin` is on your `PATH`.

## Usage

```bash
gcloud-ro compute instances list   # runs
gcloud-ro compute instances delete # denied
gcloud-ro --print-policy           # show allowed/denied verbs and flags
gcloud-ro --self-test              # run built-in tests
```

Set `GCLOUD_RO_BIN` to choose the real `gcloud` binary, or `GCLOUD_RO_DRY_RUN=1` to print approved commands without running them.

## Read-only credentials

Give the agent a service account with narrow viewer roles (for example `roles/compute.viewer`, `roles/logging.viewer`), in its own gcloud config directory so it can't use your personal login:

```bash
export CLOUDSDK_CONFIG=~/.config/gcloud-agent
gcloud auth login
gcloud config set auth/impersonate_service_account agent-ro@PROJECT.iam.gserviceaccount.com
gcloud config set project PROJECT
```

Your account needs `roles/iam.serviceAccountTokenCreator` on the service account. Then run the agent with `CLOUDSDK_CONFIG=~/.config/gcloud-agent`.
