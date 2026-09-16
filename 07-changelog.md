# 07 — Changelog

> Dated history of every meaningful change to the project. Newest at top.
> Every agent adds a line here after it changes anything. Keep each entry to 1–2 lines.
> Format: `### YYYY-MM-DD — [who]` then bullet points of what changed.

---

### 2026-09-16 — Ashna (with Claude)
- Created the shared project knowledge base (this folder) as the single source of truth for all agents.
- Filled in `01-vision-and-goals.md` with the real business goal (per Ashna): multiple keyword-rich IG pages → daily club content → user contact → lead → free-then-paid club/PR clients; end state is ~100% automated posting (timing analysis, hashtag strategy, performance feedback). Today: posting is still manual, content comes in via a Telegram channel.
- Filled in `02-architecture.md`, `03-tech-stack.md`, `04-workflows.md`, `05-conventions.md`, `08-glossary.md` with real details read directly from the live n8n workflow "Club PR - Flow B - Reactive Reel Generation": Google Sheets doc "Club PR - Asset Bank & Queue" (tabs "Sheet1" + "Posting Queue"), Cloudinary zoompan video generation (cloud `pemiahac`), Groq API used for both vision QC and SEO caption generation ("Club PR - Groq API" credential), Telegram human review routed per-club via asset_id prefix ("Club PR - Telegram Bot" credential). Some blanks remain — see `09-open-questions.md`.

---

<!-- TEMPLATE
### YYYY-MM-DD — [who / which agent]
- [ what changed ]
- [ why, if not obvious ]
-->
