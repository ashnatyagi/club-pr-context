# 07 — Changelog

> Dated history of every meaningful change to the project. Newest at top.
> Every agent adds a line here after it changes anything. Keep each entry to 1–2 lines.
> Format: `### YYYY-MM-DD — [who]` then bullet points of what changed.

---

### 2026-09-16 — Ashna (with Claude)
- Created the shared project knowledge base (this folder) as the single source of truth for all agents.
- Filled in `01-vision-and-goals.md` with the real business goal (per Ashna): multiple SEO-optimised IG pages (best practices, NO keyword stuffing) → daily club content → user contact → lead → handed free-for-a-while to PR people Ashna personally knows, then charged; end state is ~100% automated posting (timing analysis, hashtag strategy, performance feedback). Today: posting is still manual, content comes in via a Telegram channel.
- Corrected framing: pages are SEO-optimised via best practices, not "keyword-rich"/stuffing; leads go to known PR contacts free for some time then paid.
- **Full project sweep:** inspected all 5 Club PR flows in n8n and documented them as the base. Confirmed the pipeline: **Flow A** (Telegram intake + Groq auto-tagging + Cloudinary → Sheet1) → **Flow B** (reactive reels) & **Flow C** (evergreen listicles) → both append to "Posting Queue" → **Flow D** (Instagram Graph API publisher, reels + carousels, multi-page via `ig_account_id`). Plus a **Setup** flow for background music. Updated `02`, `03`, `04`, `05`, `09` accordingly; resolved the open questions about Flow A and the publishing step.
- **Added the autonomy layer** (`10-operating-principles.md`) and rewrote `AGENTS.md` + `00-START-HERE.md` so agents run the project on their own with the goal in mind, only escalating red-light maintenance items to Ashna. Logged the decision in `06-decision-log.md`.
- **Final fill-in pass:** filled the remaining blanks — Do/Don't conventions (`05`), and cleaned all leftover `[ FILL IN ]` placeholders (Known issues now read "None recorded yet", VPS gotchas point to open-questions, non-goals completed). Only the copy-template block in `04` and the tracked open questions in `09` remain by design. `README.md` now says the base is filled.
- **Correction (per Ashna):** posting is **fully automated** — Flow D runs on its own; Ashna does NOT do manual QC or manual posting. Her only manual step is pre-screening images/videos before sending them into Telegram. Updated `01`, `02`, `04`, `09`, and hardened `10-operating-principles.md` so agents never re-insert Ashna as a per-post checkpoint.
- Filled in `02-architecture.md`, `03-tech-stack.md`, `04-workflows.md`, `05-conventions.md`, `08-glossary.md` with real details read directly from the live n8n workflow "Club PR - Flow B - Reactive Reel Generation": Google Sheets doc "Club PR - Asset Bank & Queue" (tabs "Sheet1" + "Posting Queue"), Cloudinary zoompan video generation (cloud `pemiahac`), Groq API used for both vision QC and SEO caption generation ("Club PR - Groq API" credential), Telegram human review routed per-club via asset_id prefix ("Club PR - Telegram Bot" credential). Some blanks remain — see `09-open-questions.md`.

---

<!-- TEMPLATE
### YYYY-MM-DD — [who / which agent]
- [ what changed ]
- [ why, if not obvious ]
-->
