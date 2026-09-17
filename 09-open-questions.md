# 09 — Open Questions & Backlog

> Everything not yet decided or done. Agents look here for "what's next" and known risks.
> Move items into a decision (`06`) or changelog (`07`) once resolved.

## Resolved (kept for reference)
- [x] What sets a row's initial tags before Flow B? → **Flow A - Intake & Tagging** (Telegram → Groq auto-tag → Cloudinary → Sheet1).
- [x] What publishes to Instagram? → **Flow D - Posting Queue Publisher** (Instagram Graph API, reels + carousels).
- [x] Which AI powers captions? → Groq API ("Club PR - Groq API").
- [x] Is there evergreen content? → **Flow C - Evergreen Listicle Generation**.

## Resolved (more)
- [x] Is posting automated? → **Yes, fully automated.** Flow D posts to Instagram on its own; Ashna does no manual posting or checking. Her only manual step is pre-screening content before it enters Telegram.
- [x] Why does Flow B's QC feel too strict? → It isn't overly strict — it was a real bug. The Cloudinary text overlay had no width limit, so long club+event names ran off the frame edge. Fixed 2026-09-17 with `w_iw_mul_0.8,c_fit` in "Build Zoompan Video URL". See `07-changelog.md`.
- [x] Why did images/videos stop coming through Telegram? → Two compounding bugs, both fixed 2026-09-17: (1) Flow A's Telegram webhook had gone stale (fixed by unpublish/republish); (2) `qwen/qwen3.6-27b` lost Groq account access, breaking all 3 AI vision calls (fixed by switching to `qwen/qwen3.8-27b`). See `07-changelog.md`.

## To decide
- [ ] Exact Schedule Trigger intervals for Flows B, C, and D (how often each runs).

## To build / TODO
- [ ] Move toward the automation objectives in `01-vision-and-goals.md`: post-timing analysis, hashtag performance tracking, and a content performance feedback loop.
- [ ] Set up the GitHub token / access so n8n's HTTP nodes and other AI agents can pull this context automatically (see `README.md` Step 2).
- [ ] Add a backup/restore plan for the Bluehost VPS and the master Google Sheet.

## Needs a live test to close out (as of 2026-09-17)
- [ ] **Video tagging via Flow A's `Is Video For AI Tag?` node.** Two real test videos both landed in the false/needs_review branch with blank AI tags despite `source_type` clearly being `"video"` in the input — root cause not confirmed with certainty. A defensive fix is live (`String(source_type).trim().toLowerCase() === 'video'`), but it needs one more real video from Ashna to confirm end-to-end before this is considered closed. If it fails again, capture the exact raw execution JSON for the node (Executions tab → the failing run → "Is Video For AI Tag?" → JSON view) rather than trusting the on-screen "video" text in the condition editor, since that field's styling made it hard to tell a real value from a placeholder-looking render.

## Known risks
- [ ] Single Bluehost VPS = single point of failure for all n8n flows; no confirmed backup/restore plan yet.
- [ ] The Instagram access token expires and Graph API versions age out — most likely silent break point for Flow D.
- [ ] One Google Sheet is the whole database — a column rename/removal can break several flows at once.
- [ ] Human review/alerts rely on someone watching Telegram — no fallback if a message is missed.
- [ ] **Groq vision model availability is not stable** — `qwen/qwen3.6-27b` lost account access without a formal deprecation notice on 2026-09-17, breaking all 3 vision calls (Flow A image + video tagging, Flow B QC) at once since they share one credential/model name. Now on `qwen/qwen3.8-27b`; if that also breaks, check `console.groq.com/docs/models` and `/docs/deprecations` for the current vision-capable model and update all 3 nodes together.
- [ ] Telegram webhooks on this Bluehost n8n instance can silently go stale (Flow A went 24+ hours with zero executions on 2026-09-16/17) with no alerting — worth checking periodically or adding a heartbeat/monitor if this recurs.
