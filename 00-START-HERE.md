# 📌 Club PR — Project Knowledge Base (START HERE)

> This folder is the **single source of truth** for the Club PR automation project.
> Humans AND AI agents (Claude, ChatGPT, n8n AI nodes, etc.) read and update these files.
> If it isn't written here, it doesn't exist. If you change the system, update the file + add a changelog line.

---

## Rules for any AI agent working on this project

1. **Read this file first.** Then read ONLY the file relevant to your task (do not re-read everything — that wastes tokens).
2. **Never guess.** If context is missing, check the file that should have it. If it's still missing, ask the human and then write the answer into the right file.
3. **After making a change** to any workflow, tool, or decision:
   - update the relevant file (e.g. `04-workflows.md`, `03-tech-stack.md`)
   - add a dated entry to `07-changelog.md`
4. **Keep entries short and factual.** This is a reference, not an essay.
5. **One topic = one file.** Don't scatter the same fact across files.

## Map of this knowledge base

| File | What's inside | Read it when… |
|------|---------------|---------------|
| `00-START-HERE.md` | This index + rules | Always, first |
| `01-vision-and-goals.md` | Long-term goal, objectives, success metrics | You need the "why" / priorities |
| `02-architecture.md` | How the whole system fits together, end to end | You need the big picture |
| `03-tech-stack.md` | Every tool, what it's for, how we use it | You need to know a tool or credential |
| `04-workflows.md` | Every n8n flow, step by step | You're building/fixing a specific flow |
| `05-conventions.md` | Naming rules, standards, do's & don'ts | Before you create or rename anything |
| `06-decision-log.md` | Why key choices were made | You're about to reverse or question a decision |
| `07-changelog.md` | Dated history of every change | You need to know what changed and when |
| `08-glossary.md` | Terms, acronyms, internal names | You hit a word you don't recognise |
| `09-open-questions.md` | Backlog, TODOs, unknowns, risks | You're looking for what's next / unresolved |
| `10-operating-principles.md` | **How to run this project autonomously** — what to do without asking vs. when to pull in Ashna | **Always, if you're an agent doing work** — this is your rulebook |
| `AGENTS.md` | The short "primer" you paste into any new agent | Setting up a new agent/thread |
| `README.md` | How to host this + give agents access | Setting up / maintaining the KB |

> **If you're an AI agent here to DO work (not just answer a question):** read `01-vision-and-goals.md` (the why) and `10-operating-principles.md` (how to act on your own) before you start. Those two make you useful without Ashna in the loop.

## How to load this into different tools

- **Claude / ChatGPT (new thread):** paste the contents of `AGENTS.md`, or give it the link to this folder if the tool can browse.
- **n8n AI Agent node:** add an HTTP Request node at the top that fetches the raw file(s) you need, and pass the text into the agent's system prompt. (See `README.md`.)
- **A human teammate:** just open the folder.
