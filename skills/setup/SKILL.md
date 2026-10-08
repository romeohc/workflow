---
name: setup
description: Install this repo's workflow. Environment, local, radar, CI, deploy, and the radar automation. Skip what already matches.
disable-model-invocation: true
---

# Setup

Read the repo. Skip a piece that already matches. Update a piece when the app has a service it does not describe.

Do not copy `hard`, `fast`, `debug`, `think`, or `clean` into the repo. Those are the user's Cursor skills.

No app yet (no manifest and no start script): stop. Say the repo has no app. Do not invent one.

## Environment

Write `.cursor/environment.json` from how this app actually starts. Install command. One terminal per long-running process. The ports those processes listen on. Do not invent a service that is not in the repo.

You write the file. The user validates the cloud environment in the Cursor console when none is saved yet. You do not paste secrets.

## Local

Write `.cursor/skills/local/SKILL.md`. Description: how to use this app on this VM. Read every time you need the running app.

Read how this app really boots. Write one concrete line per process, in boot order. Each line has the real command, the port, and the ready log or health URL. Also write the waits that must finish before the next step, when the repo has them: empty data on boot, a seed after the ready log, a webhook secret before checkout, a test account that is reused, a provider that must not be used. Sign-in, only if the repo already has one: the URL, the steps, the env var names, and nothing invented. End with `HTTP: curl the port you need.`

Do not add a process this app does not run. Do not turn a concrete step into a summary.

## Radar

Write `.cursor/skills/radar/SKILL.md` with `disable-model-invocation: true`. The rules below are copied into that skill word for word. Only the hosts, URLs, app names, project ids, path filters, and log lines to ignore change to match this repo. The skill contains one script. The agent who runs it does not rewrite it and does not look up endpoints.

Rules:

- Read only. This SHA of `origin/main` only. Fetch and compare on every poll. If main moved, reply `🚫` and stop.
- Check public health, each real host, and the main `push` CI run named `CI` in the same turn. Always read the latest live deploy, even if this SHA did not trigger one.
- The `push` CI run named `CI` for this SHA must be completed and green before `✅`, even when this commit deployed nothing. The script polls it about every 20s and stops around 4 minutes. It prints `ci_wait` with the conclusion. Do not reply while that run is in progress. If it is still running at the cap, say so and stop. If `origin/main` moved while waiting, reply `🚫` and stop.
- `✅` is the whole reply, and only when that CI run is green and every host checked is green. A missing secret: name it and stop. Do not invent `✅`.
- A real error on this tip (deploy failed, prod down, CI red, or an error in the first ~20s of logs after a new deploy of this SHA): Engineer with the debug skill if you don't know why, then one draft fix PR. Do not deploy. Prose only when you Engineer.
- Skip the wait, never the look, when this SHA did not touch that host's paths. Read those paths from this repo. A Vercel `CANCELED` ignoreCommand is green. PR CI is not prod.
- One page of logs is the look. Sit ~20s and read that host again only after a new deploy of this SHA just succeeded on it.
- Ignore only log lines this repo already shows are noise. Do not copy another app's quirks.

The script, from the repo root, one run:

- Wave 1, all at once: `origin/main` and its file list, the public health URLs, one CI snapshot, one status and one log page per host that exists.
- Then `wait_ci`: poll until the `CI` run for this SHA is completed, or 4 minutes pass, or main moves. Print `ci_wait` and the final runs.
- Wave 2, all at once, only what needs an id: the Vercel build log for this SHA and for the latest `READY`, the Convex log page, and a second load of the home page.

Host calls, only when that host exists:

- Vercel. Bearer `VERCEL_TOKEN`. Resolve the project id and team id once and write them in the script. List production deployments, then events for this SHA and the latest `READY`.
- Fly. One app name per `fly.toml`. `Authorization` is the raw `FLY_API_TOKEN` (`FlyV1`, no `Bearer`). Machines, then one log page.
- Convex prod. `POST https://api.convex.dev/api/deployment/url_for_key` with `CONVEX_PROD_DEPLOY_KEY` inside the script. Then `GET {url}/version` and one page of `GET {url}/api/stream_function_logs?cursor=0` with header `Authorization: Convex <key>`. Never `npx convex logs`. Never write the key into `.env.local`. Never set `CONVEX_DEPLOY_KEY`.

Name a secret only for a host this app uses: `VERCEL_TOKEN`, `FLY_API_TOKEN`, `CONVEX_PROD_DEPLOY_KEY`.

Then, with the automate skill, one automation: push to `main` on this repo, and the prompt only runs the radar skill on that SHA. If that automation already exists, leave it.

## CI

Write `.github/workflows/ci.yml`. On pull requests and pushes to `main`. One job per package that already has a check. Install, then lint, test, and typecheck, only the scripts that exist. Do not add a check the repo has no script for.

## Deploy

Vercel, only if this repo deploys a frontend there. Production on `main` only. Skip the build when the commit did not touch the frontend, Convex, or the root package files. If Convex is in the repo, the production build runs `npx convex deploy`, then the app build.

Fly, only if a `fly.toml` exists. One workflow per app, on push to `main`, path-filtered to that app. `flyctl deploy --remote-only`. Token from the GitHub secret `FLY_API_TOKEN`.

Do not add a host the repo does not use.

## After

Reply with what you added, what you skipped, and the keys still missing in this project's Cursor environment. Only name a key this app's hosts need. The user pastes them. You do not create tokens.

If no cloud environment is saved yet, say so. The user validates it in the Cursor console, then pastes the keys.
