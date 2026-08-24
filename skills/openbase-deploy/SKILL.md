---
name: openbase-deploy
description: >-
  Use this skill when deploying an app to Openbase Cloud (the PaaS): connecting a
  GitHub repository, push-to-deploy for backend and frontend, managing config
  vars and secrets, adding hostnames/domains, or inspecting apps, logs, releases,
  usage, and spend with the `openbase` CLI or the web dashboard.
version: 1.0.0
---

# Deploy with Openbase Cloud

Openbase Cloud is a Heroku-style platform (PaaS). You connect a GitHub
repository to an app; pushing to the connected branch builds and deploys it. You
manage everything from the web dashboard at
[app.openbase.cloud](https://app.openbase.cloud) or from the terminal with the
`openbase` CLI. Both talk only to the Openbase Cloud API over HTTPS — there is no
infrastructure to run yourself.

## Mental model

- **App** — a deployed backend and/or frontend, addressed by name.
- **Deploy (release)** — one build+release of an app. Triggered by a push to the
  connected GitHub branch (or re-run from the dashboard). Each deploy produces a
  numbered release you can inspect or roll back to.
- **Config vars / secrets** — environment variables for the app. Secret values
  are write-only: you can set them, but they are never displayed back.
- **Collaborator** — a user invited to an app's server pool. Collaborators can
  deploy and manage config vars/secrets for the pool's apps; only the owner
  manages collaborator access (`openbase access`, or the pool page in the
  dashboard).
- **Collaborator** — a user invited to an app's server pool. Collaborators can
  deploy and manage config vars/secrets for the pool's apps; only the owner
  manages collaborator access (`openbase access`, or the pool page in the
  dashboard).
- **Hostname** — a domain the app serves on (an Openbase subdomain by default;
  custom domains can be added).
- **Usage / spend** — deployments accrue metered spend against your account's
  monthly limit; see `openbase usage` or the dashboard.

## Deploy an app

1. **Create the app and connect GitHub** from the dashboard: choose the
   repository and the branch to deploy from (commonly `main`).
2. **Set config vars and secrets** for the app before the first deploy if the
   app needs them (database URL, API keys, etc.).
3. **Push to the connected branch.** Openbase builds the backend and, if the app
   has a frontend, builds and publishes the frontend assets, then releases.
4. **Watch the deploy** in the dashboard or with `openbase logs -a <app>`.
5. **Add a custom hostname** from the dashboard if you don't want the default
   Openbase subdomain.

Backend and frontend both deploy from the same GitHub push — there is no
separate manual publish step and nothing to build or upload from your laptop.

## `openbase` CLI

Install and sign in:

```bash
pip install openbase-cli        # provides the `openbase` command
# or: uv tool install openbase-cli
openbase login                  # shared login with openbase-coder
openbase whoami                 # signed-in email
```

Login is shared with the `openbase-coder` CLI — signing in with either signs you
in for both, storing credentials in `~/.openbase/auth.json`.

Address an app with `-a/--app <name>`, or set `OPENBASE_APP` in your
environment.

```bash
openbase apps                   # list your apps
openbase ps -a my-app           # current status (alias: status)
openbase logs -a my-app         # recent logs
openbase logs -a my-app --tail  # stream new lines (Ctrl-C to stop)
openbase config -a my-app       # config vars (secret values hidden)
openbase config set -a my-app K=V           # set plaintext vars, redeploys
openbase config set --secret -a my-app K=V  # set write-only secrets
openbase access -a my-app                   # pool owner + collaborators
openbase access add -a my-app a@b.com       # invite a collaborator (owner)
openbase access remove -a my-app a@b.com    # remove/revoke (owner)
openbase releases -a my-app     # recent deploys
openbase open -a my-app         # open the app in your browser
```

Account, projects, and workspaces:

```bash
openbase account                # balance, subscription
openbase usage                  # this month's spend and limits
openbase projects               # your Openbase Cloud projects
openbase workspaces             # your cloud dev workspaces (devspaces) and status
```

Add `--json` to most commands for scripting. Anything not covered by a
first-class command can be run against the Coder CLI via `openbase coder <args>`.

## Configuration

| Variable | Purpose | Default |
| --- | --- | --- |
| `OPENBASE_APP` | Default app for `-a` | — |
| `OPENBASE_API_URL` | Override the Openbase Cloud base URL | `https://app.openbase.cloud` |
| `OPENBASE_HOST` | Alias for `OPENBASE_API_URL` | — |

## Troubleshooting a deploy

- **Deploy didn't start after a push** — confirm the app is connected to the
  right repository and branch, and that the push landed on that branch.
- **Build failed** — read `openbase logs -a <app>` (or the dashboard deploy
  log). Backend build failures are usually a missing dependency; frontend
  failures are usually a wrong build command, app root, or output directory in
  the frontend settings.
- **App runs but returns errors** — check `openbase config -a <app>` for missing
  config vars, and the runtime logs.
- **Frontend loads but can't reach the API / CORS errors** — the frontend is
  pointed at the wrong API origin or a hostname isn't attached; check the app's
  hostnames in the dashboard.
- **Deploy blocked or throttled** — check `openbase usage`; you may have hit the
  monthly spend limit.

## Safety

- Never paste secret values into shared logs or chat. `openbase config` hides
  secret values by design; keep it that way.
- Rolling back or re-deploying is safe and reversible. Deleting an app or its
  data is not — confirm before destructive dashboard actions.
