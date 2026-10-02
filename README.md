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

## Recommended setup

`gcloud-ro` is a client-side filter, not a security boundary. An agent that can run `gcloud`, `curl`, or a client library directly bypasses it. Pair it with credentials that can only read:

1. Create a dedicated service account with narrow read roles (for example `roles/compute.viewer`, `roles/logging.viewer`). Avoid roles that read sensitive data, such as Secret Manager access or Cloud Storage object reads, unless needed.

2. Give the agent its own gcloud config directory that impersonates that account, so it can't fall back to your personal login:

   ```bash
   export CLOUDSDK_CONFIG=~/.config/gcloud-agent
   gcloud auth login
   gcloud config set auth/impersonate_service_account agent-ro@PROJECT.iam.gserviceaccount.com
   gcloud config set project PROJECT
   ```

   Your account needs `roles/iam.serviceAccountTokenCreator` on the service account.

3. Run the agent with `CLOUDSDK_CONFIG=~/.config/gcloud-agent` and only `gcloud-ro` in its allowlist.

IAM limits what is possible; `gcloud-ro` blocks token printing, SSH, config changes, and impersonation overrides.

## Environment

- `GCLOUD_RO_BIN` — real `gcloud` binary (default: first `gcloud` on `PATH`)
- `GCLOUD_RO_DRY_RUN=1` — print the approved command and exit without running it
