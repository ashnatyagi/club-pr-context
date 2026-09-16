# README — How to host this and give every agent access

This folder is your shared "project brain". The goal: **one place, many agents.** You edit once, and Claude, ChatGPT, your n8n AI nodes, and any teammate all read the same up-to-date context — so you stop re-explaining the system and stop wasting tokens.

## Step 1 — Pick where it lives (choose ONE main home)

**Option A — GitHub repo (recommended).**
- Create a private repo, drop this folder in, commit.
- ✅ Free version history = your changelog is automatic (every commit is dated).
- ✅ Any agent can read a file by its **raw URL** (`https://raw.githubusercontent.com/<you>/<repo>/main/03-tech-stack.md`).
- ✅ You (and teammates) edit from anywhere.

**Option B — Your Bluehost VPS as web files.**
- Put this folder somewhere web-served, e.g. `https://your-domain/context/`.
- Any agent fetches by URL. Good since your VPS is already running.
- ⚠️ Keep it behind a token or basic-auth if it shouldn't be public.

**Option C — Shared cloud drive (Google Drive / Notion / Dropbox).**
- Easiest for non-technical teammates.
- Works for AIs that have a connector to that drive; for n8n you'd fetch via that service's API.

You can also do A **and** mirror to B — GitHub as the master, VPS as the fast fetch source.

## Step 2 — Give your n8n agents access

At the **top of any n8n AI workflow**, add an **HTTP Request** node that fetches the file(s) the agent needs (e.g. `00-START-HERE.md` + the relevant `04-workflows.md`), then pass that text into the **System Prompt** of your AI Agent node. Now the agent has the context every run, and reads only the small relevant files — not "the whole system".

> Tip: don't dump all 12 files into every agent. Fetch just the 1–2 files that agent needs. That's what keeps tokens low.

## Step 3 — Give Claude / ChatGPT access

- New thread → paste `AGENTS.md`, or give the raw folder/file link if the tool can browse the web.
- Ask it to read only the relevant file for the task.

## Step 4 — Keep it alive (the important habit)

The knowledge base only works if it stays current. The rule (also in `00-START-HERE.md`):
> **Whenever anything changes → update the relevant file + add one line to `07-changelog.md`.**

You can even make this a job: tell each agent "before you finish, update the changelog." Over time this becomes the memory that survives every thread and agent switch.

## What's in here
```
00-START-HERE.md      index + rules for agents (read first)
01-vision-and-goals.md long-term goal, objectives, metrics
02-architecture.md     how the whole system fits together
03-tech-stack.md       every tool + how we use it
04-workflows.md        every n8n flow, step by step
05-conventions.md      naming, standards, do's & don'ts
06-decision-log.md     why key choices were made
07-changelog.md        dated history of changes
08-glossary.md         terms & internal names
09-open-questions.md   backlog, TODOs, risks
AGENTS.md              the primer you paste into any new agent
README.md              this file
```

## Next: fill in the `[ FILL IN ]` blanks
Most files have `[ FILL IN ]` placeholders. Spend 30–40 minutes filling them once — after that, you (or an agent) only edit small bits as things change.
