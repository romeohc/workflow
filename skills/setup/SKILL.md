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

Write `.cursor/skills/local/SKILL.md`. How to boot this app on the VM, in order, with the real commands and ports. Include sign-in or health checks the repo already has.

Description: how to use this app on this VM. Read every time you need the running app.

## Radar

Write `.cursor/skills/radar/SKILL.md` for the hosts this repo really deploys to. `disable-model-invocation: true`.

Read only while you check. This SHA of `origin/main` only. Fetch and compare on every poll. If main moved, reply '🚫' and stop. Check public health, each real host, and main `push` CI in the same turn. Always read the latest live deploy, even if this SHA did not trigger one. Poll only a new deploy of this SHA, about every 20s, stop around 4 minutes.

All green: reply '✅' and stop. Incomplete or a missing secret: say so and stop. Do not patch. A real error on this tip: Engineer (debug skill if you don't know why), then one draft fix PR. Do not deploy. '✅' and '🚫' are the whole reply.

Secrets in the Cursor environment, not git. Name only the hosts that exist:

- Vercel: `VERCEL_TOKEN`
- Fly: `FLY_API_TOKEN`
- Convex prod: `CONVEX_PROD_DEPLOY_KEY`. Never set `CONVEX_DEPLOY_KEY`. Pass it only on the child command.

Skip the wait, never the look, when this SHA did not touch that host's paths. A Vercel `CANCELED` ignoreCommand is green. PR CI is not prod. Main CI is the `push` run on `main`.

Fill public URLs, path filters, and log quirks from this repo. Do not copy another app's hosts.

## CI

Write `.github/workflows/ci.yml`. On pull requests and pushes to `main`. One job per package that already has a check. Install, then lint, test, and typecheck, only the scripts that exist. Do not add a check the repo has no script for.

## Deploy

Vercel, only if this repo deploys a frontend there. Production on `main` only. Skip the build when the commit did not touch the frontend, Convex, or the root package files. If Convex is in the repo, the production build runs `npx convex deploy`, then the app build.

Fly, only if a `fly.toml` exists. One workflow per app, on push to `main`, path-filtered to that app. `flyctl deploy --remote-only`. Token from the GitHub secret `FLY_API_TOKEN`.

Do not add a host the repo does not use.

## Automation

Use the automate skill. One automation: push to `main` on this repo, and the prompt runs the radar skill on that SHA. If that automation already exists, leave it.

## After

Reply with what you added, what you skipped, and the keys still missing in this project's Cursor environment. Only name a key this app's hosts need. The user pastes them. You do not create tokens.

If no cloud environment is saved yet, say so. The user validates it in the Cursor console, then pastes the keys.
