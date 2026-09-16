# AGENTS.md — paste this into any new agent / thread

You are working on the **Club PR** project. This knowledge base is your single source of truth and your brain — use it instead of asking Ashna to re-explain anything.

## Before you start
1. Read `00-START-HERE.md` (rules + map of the KB).
2. Read `01-vision-and-goals.md` (the goal — always keep it in mind).
3. Read `10-operating-principles.md` (how to work on your own, and when to stop for Ashna).
4. Then read ONLY the file(s) relevant to your task — don't read everything (wastes tokens/time).

## How to behave (short version)
- **The goal:** grow SEO-optimised Instagram pages that generate club/PR leads, and push the pipeline toward ~100% automation — **without dropping quality. SEO = best practices, never keyword stuffing.**
- **Act, don't wait.** Do the safe work yourself. Ashna wants to step in only for maintenance when it's truly needed.
- **Green light (do it):** read/diagnose flows and the sheet, draft content/captions, propose flow improvements as drafts, investigate failures, and always update the KB + `07-changelog.md` when anything changes.
- **Red light (stop, ask Ashna, one clear specific ask):** publishing live, changing a live flow's real behaviour, anything with credentials/tokens/money, deleting data or changing the sheet's columns, or an unsure brand/quality call.

## The system in 6 lines
- Automation runs on **n8n**, self-hosted on a **Bluehost VPS**. Workflows are named `Club PR - Flow [Letter] - [Purpose]`.
- One **Google Sheet** ("Club PR - Asset Bank & Queue") is the database: tab **Sheet1** = tagged assets, tab **Posting Queue** = ready-to-post.
- **Flow A** ingests + AI-tags content from Telegram → Sheet1.
- **Flow B** makes event reels, **Flow C** makes evergreen listicles → both append to Posting Queue.
- **Flow D** publishes the queue to **Instagram (Graph API)** — reels + carousels, multiple pages via `ig_account_id`.
- AI = **Groq** (tagging, QC, captions). Media = **Cloudinary**. Human alerts + input = **Telegram**.

Before you finish: put anything durable you learned or changed into the right KB file + `07-changelog.md`, so the next agent picks up exactly where you left off.
