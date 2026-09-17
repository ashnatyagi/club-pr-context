# 07 — Changelog

> Dated history of every meaningful change to the project. Newest at top.
> Every agent adds a line here after it changes anything. Keep each entry to 1–2 lines.
> Format: `### YYYY-MM-DD — [who]` then bullet points of what changed.

---

### 2026-09-17 — Claude (with Ashna)
- **Root-caused the "QC feels too strict" complaint:** Flow B's Groq Vision QC only judges whether the burned-in club/event text is legible/not cut off — it never re-judges Ashna's pre-screened poster image itself. The real defect was Cloudinary's text overlay having no width limit, so long club+event names ran off the frame edge at the smallest font size. Fixed in Flow B's "Build Zoompan Video URL" node: the `l_text:` layer now carries `w_iw_mul_0.8,c_fit` so long text wraps within 80% of the image's own width instead of overflowing. Verified visually on a real long-name poster and a short-name poster (no regression). Published to Flow B (now **live**, not draft).
- **Found and fixed Flow A's Telegram trigger going silent:** no executions for 24+ hours (last run 15 Sep 13:17). Unpublished + republished Flow A to force n8n to re-register the webhook with Telegram's Bot API — confirmed fixed when 4 backlogged messages flooded in within the same second right after republish.
- **Found and fixed a Groq model access loss affecting all 3 AI vision calls:** `qwen/qwen3.6-27b` started returning "model does not exist or you do not have access to it" — hit Flow A's image tagging, Flow A's video tagging (`Groq Video Tag`), and Flow B's `Groq Vision QC Check` simultaneously, since all three share one Groq credential and model name. This explains earlier image-tagging failures too, not just video. Switched all three nodes to `qwen/qwen3.8-27b` (Groq's other current documented vision model) and republished Flows A and B. Verified live: retried the 4 backlogged executions against the fixed config — all 4 succeeded with correctly-tagged rows (confidence 0.95–0.98) in Sheet1.
- **Found (not yet fully confirmed) a third, separate video-tagging bug:** even after the webhook + Groq-model fixes, two consecutive real test videos from Ashna both landed in Flow A's `Is Video For AI Tag?` (If) node's **false** branch with `source_type: "video"` clearly present in the input — sending the row to `needs_review` with every AI tag blank instead of running `Groq Video Tag`. Direct inspection of the condition (`{{$json.source_type}}` is equal to `video`) and of the upstream code (`Code in JavaScript3`, which sets `sourceType = isVideo ? 'video' : ...`) found nothing visibly wrong — both sides looked like a clean string match. Root cause not pinned down with certainty (n8n's test-execution tooling can't easily replay a Telegram-Trigger workflow node-by-node without either a live message or fully mocking the binary file chain). Applied a defensive fix instead: the condition now reads `String($json.source_type || '').trim().toLowerCase()` compared to `video`, which absorbs whitespace/case/type-coercion edge cases regardless of the exact original cause. Published to Flow A. **Status: awaiting one more live test video from Ashna to confirm — see `09-open-questions.md`.**
- Session notes/evidence for all of the above are also in the AI assistant's own memory (`/areas/pr-business-automation.md`) and in the persisted report artifact shared with Ashna.

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
