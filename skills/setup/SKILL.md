---
name: setup
description: Install this repo's workflow. Check each piece. Send an agent for each one that is missing.
disable-model-invocation: true
---

# Setup

You check this app against the pieces below. You do not write them yourself.

Do not copy `hard`, `fast`, `debug`, `think`, or `clean` into the repo. Those are the user's Cursor skills.

No app yet (no manifest and no start script): stop. Say the repo has no app. Do not invent one.

## Do

Read the repo. For each piece, decide if it already matches, including every service this app really has. A piece that matches: skip it. Do not send an agent.

A piece that is missing, or that misses a service this app has: send one agent to put that piece in place. Send every such piece at once. Each agent owns one piece and follows only that section.

## Environment

Write `.cursor/environment.json` from how this app actually starts. Install command. One terminal per long-running process. The ports those processes listen on. Do not invent a service that is not in the repo.

You write the file. The user validates the cloud environment in the Cursor console when none is saved yet. You do not paste secrets.

## Local

Write `.cursor/skills/local/SKILL.md`. Description: how to use this app on this VM. Read every time you need the running app.

Read how this app really boots. Write one concrete line per process, in boot order. Each line has the real command, the port, and the ready log or health URL. Also write the waits that must finish before the next step, when the repo has them: empty data on boot, a seed after the ready log, a webhook secret before checkout, a test account that is reused, a provider that must not be used. Sign-in, only if the repo already has one: the URL, the steps, the env var names, and nothing invented. End with `HTTP: curl the port you need.`

Do not add a process this app does not run. Do not turn a concrete step into a summary.

## Radar

Skip it when this app deploys nothing to production.

Write `.cursor/skills/radar/SKILL.md` with `disable-model-invocation: true`. Description: after main moves, check that this tip of main is healthy in production, and fix it if not. The skill has three parts.

Rules, copied as is:

- Read only while you check. This SHA of `origin/main` only. If main moved, reply `🚫` and stop.
- Run the command once. Do not rewrite it. Do not look anything up. Its last line is the verdict.
- `GREEN`: `✅` is the whole reply. `MOVED`: `🚫`. `MISSING <secret>`: name it and stop. `TIMEOUT`: say what is still running and stop.
- `RED`: if the error names its cause, fix it. If not, use the debug skill. Then one draft fix pull request. Do not deploy. Prose only here.
- A red that is only noise: the fix adds that line to the command's filter. A host that no longer answers the same way: the fix updates the command.

Map: one line per production surface this repo really has: frontend, backend, database, workers. Each line has its name, the paths that deploy it, where its deploy status lives, its logs, its health URL, and the secret it needs. Then the CI checks a push to `main` starts on this repo's forge, if it has any. Read all of it from this repo's host configs, CI config, and deploy config. Do not add a surface or a check this app does not have.

Run: one command, from the repo root, every call in parallel. It:

- fetches `origin/main`, prints the SHA and its files, and marks each surface as touched or not.
- waits until every check in the map has started and completed on this SHA, about every 20s, and stops around 4 minutes. It stops with `MOVED` if main moves.
- for every surface: prints live deploy status, one page of logs, and the health URL answer.
- for a touched surface: waits for this SHA's deploy there, then waits ~20s and reads its logs again.
- ends with one verdict line: `GREEN`, `MOVED`, `MISSING <secret>`, `TIMEOUT`, or `RED <surface> <reason>`.

Write ids and endpoints into the command once. Secrets come from the Cursor environment.

Run the command once on the current main. It must end with a verdict and print every surface.

Then, with the automate skill, one automation named `Radar - <app name>`, on push to `main` on this repo. The prompt is exactly `Use radar skill in /workspace/.cursor/skills/radar/SKILL.md. Follow this skill and reply in french.` The automation reads the skill from `main`.

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

Radar added: one line names each secret still missing in the Cursor environment, `radar: paste <SECRET>`. Radar not on `main` yet: `radar: merge to main to start`.

A piece you tried did not work. Reply in prose. Say what failed and why. This is the only time you explain.

`✅` and `⚙️` are the whole reply. Do not invent `✅` when you changed the app, or when something failed.
