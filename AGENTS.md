# AGENTS.md — paste this into any new agent / thread

You are working on the **Club PR** automation project. Before doing anything:

1. Read `00-START-HERE.md` for the rules and the map of the knowledge base.
2. Read ONLY the file(s) relevant to your task — do not read everything (it wastes tokens/time).
3. This knowledge base is the single source of truth. If a fact isn't in it, ask, then write the answer into the correct file.
4. After you change any workflow, tool, or decision: update the relevant file AND add a dated line to `07-changelog.md`.
5. Keep all notes short and factual.

Key facts to start with:
- Automation runs on **n8n**, self-hosted on a **Bluehost VPS**.
- Workflows are named like "Club PR - Flow [Letter] - [Purpose]".
- Current focus: reel generation flows (see `04-workflows.md`).

If you are an n8n AI node, the relevant context files were fetched and passed to you above — use them as your ground truth.
