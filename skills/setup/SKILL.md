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

Write `.cursor/skills/radar/SKILL.md` with `disable-model-invocation: true`. Description: After main moves, check that everything this app deploys matches main. The rules below are copied into that skill word for word.

Rules:

- Read the repo. The check covers every service this app deploys, and the tests it actually runs. A service the repo does not use is out.
- You own the current `origin/main`. Fetch at the start and before the verdict. If main moved, reply `🛑` and stop. That emoji is the whole reply.
- For each deployed service, ask which commit is live. Diff that commit with main, only on the files of that service.
- No diff: that service already matches main. Skip it.
- A diff: wait until that deploy is live, or it has failed. If main moves while you wait, reply `🛑` and stop.
- For each service that just deployed: read its logs since that deploy. Open its public address when it has one.
- Run the tests this app has for this main.
- All of that is green: reply `✅` and stop. That emoji is the whole reply. Do not invent `✅`.
- Something failed and the cause is clear: fix it. The cause is unknown: use the debug skill, then fix it.

Then, with the automate skill, one automation named `Radar - <app name>`: push to `main` on this repo. The prompt is exactly `Use radar skill in /workspace/.cursor/skills/radar/SKILL.md. Follow this skill and reply in french.` A newer push cancels the running one. If that automation already exists, leave it.

## CI

Write `.github/workflows/ci.yml`. On pull requests and pushes to `main`. One job per package that already has a check. Install, then lint, test, and typecheck, only the scripts that exist. Do not add a check the repo has no script for.

## Deploy

Vercel, only if this repo deploys a frontend there. Production on `main` only. Skip the build when the commit did not touch the frontend, Convex, or the root package files. If Convex is in the repo, the production build runs `npx convex deploy`, then the app build.

Fly, only if a `fly.toml` exists. One workflow per app, on push to `main`, path-filtered to that app. `flyctl deploy --remote-only`. Token from the GitHub secret `FLY_API_TOKEN`.

Do not add a host the repo does not use.

## Reply

The whole reply is one of these.

This app already matches this skill. You changed nothing. Reply `✅`.

You put pieces in place and every one worked. Reply `⚙️` and one short line per piece you added or updated. More than one line is a bullet list under that emoji. `local skill added`. Do not list what you skipped. Do not explain.

A piece you tried did not work. Reply in prose. Say what failed and why. This is the only time you explain.

`✅` and `⚙️` are the whole reply. Do not invent `✅` when you changed the app, or when something failed.
