# Workflow

The same way of working on every app. Install it once. It is not copied into application repos.

## Skills

| Skill | What it does |
| --- | --- |
| `/setup` | Installs the workflow on the current app. Skips what is already in place. |
| `/fast` | Implements the change. No verification. |
| `/hard` | Implements the change and proves it on the running app. |
| `/debug` | Finds an unknown cause from runtime evidence. |
| `/think` | Read-only. Product and engineering judgment. |
| `/clean` | Read-only cleanup proposals. Implements only the one you name. |

`local` and `radar` are written into each app by `/setup`. They describe that app.

## Install

**Cursor.** Customize, then import this GitHub repository.

**Claude Code.**

```text
/plugin marketplace add romeohc/workflow
/plugin install workflow@workflow
```

**Codex.**

```text
codex plugin marketplace add romeohc/workflow
```

Then install `workflow` from `/plugins`.

## Use

On an app, run `/setup` and say nothing else.

It writes the Cursor environment file, `local`, `radar`, CI, automatic deploy, and the automation that runs radar after a merge to `main`. Vercel and Convex are included when the app uses them. Fly is included only when a `fly.toml` exists.

When it finishes, it lists the keys to paste into that project's Cursor environment. Only the hosts this app uses:

- `VERCEL_TOKEN`
- `FLY_API_TOKEN`
- `CONVEX_PROD_DEPLOY_KEY`

If no cloud environment is saved yet, validate it in the Cursor console, then paste the keys.

## Update

Push a change to this repository. Update the plugin in each tool. The skills update everywhere. Run `/setup` again on an app only when its deploy, CI, or local setup should change.
