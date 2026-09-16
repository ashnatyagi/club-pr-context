# 09 — Open Questions & Backlog

> Everything not yet decided or done. Agents look here for "what's next" and known risks.
> Move items into a decision (`06`) or changelog (`07`) once resolved.

## Resolved (kept for reference)
- [x] What sets a row's initial tags before Flow B? → **Flow A - Intake & Tagging** (Telegram → Groq auto-tag → Cloudinary → Sheet1).
- [x] What publishes to Instagram? → **Flow D - Posting Queue Publisher** (Instagram Graph API, reels + carousels).
- [x] Which AI powers captions? → Groq API ("Club PR - Groq API").
- [x] Is there evergreen content? → **Flow C - Evergreen Listicle Generation**.

## To decide
- [ ] Exact Schedule Trigger intervals for Flows B, C, and D (how often each runs).
- [ ] Live status of each flow — Flow D showed an un-toggled "Publish" button on 2026-09-16 (draft?). Confirm which flows are actually running vs. run manually.
- [ ] Where does posting sit today: is Flow D running automatically, or is Ashna still posting by hand while Flow D is being finished?

## To build / TODO
- [ ] Move toward the automation objectives in `01-vision-and-goals.md`: post-timing analysis, hashtag performance tracking, and a content performance feedback loop.
- [ ] Set up the GitHub token / access so n8n's HTTP nodes and other AI agents can pull this context automatically (see `README.md` Step 2).
- [ ] Add a backup/restore plan for the Bluehost VPS and the master Google Sheet.

## Known risks
- [ ] Single Bluehost VPS = single point of failure for all n8n flows; no confirmed backup/restore plan yet.
- [ ] The Instagram access token expires and Graph API versions age out — most likely silent break point for Flow D.
- [ ] One Google Sheet is the whole database — a column rename/removal can break several flows at once.
- [ ] Human review/alerts rely on someone watching Telegram — no fallback if a message is missed.
