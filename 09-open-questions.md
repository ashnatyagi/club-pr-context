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

## Resolved (more, 2026-09-19)
- [x] **One real video through Flow A, end to end.** Confirmed working — execution #24649 (17 Sep) wrote a fully-tagged row for `1063557449_1918` (ILLUZION LUXE CLUB) to Sheet1. `Parse Video Tag Result` has now executed for real.
- [x] Why did Flow B (video generation) make no visible progress after that? → Unrelated bug: `Generate SEO Caption` was still on the deprecated `qwen/qwen3.6-27b` model (missed in the 17 Sep migration), which crashed every Flow B execution before the video branch could run. Fixed 19 Sep — see `07-changelog.md`.

## To decide (new, 2026-09-19)
- [ ] **Should videos be able to auto-post at all, or is manual Telegram review always intended for them?** Right now every video, regardless of tagging confidence/fast_track, is routed by `Is Video For Posting?` to `Notify Non-Image Asset` (manual review) — the `Prepare Video For Posting` "post as-is" branch exists in the workflow but its condition never fires for any current row. If auto-posting for high-confidence/fast-tracked videos is wanted, that condition needs to be built out and tested the same way the image path was.

## Known risks
- [ ] Single Bluehost VPS = single point of failure for all n8n flows; no confirmed backup/restore plan yet.
- [ ] The Instagram access token expires and Graph API versions age out — most likely silent break point for Flow D.
- [ ] One Google Sheet is the whole database — a column rename/removal can break several flows at once.
- [ ] Human review/alerts rely on someone watching Telegram — no fallback if a message is missed.
- [ ] **Groq vision model availability is not stable** — `qwen/qwen3.6-27b` lost account access without a formal deprecation notice on 2026-09-17, breaking all 3 vision calls (Flow A image + video tagging, Flow B QC) at once since they share one credential/model name. Now on `qwen/qwen3.8-27b`; if that also breaks, check `console.groq.com/docs/models` and `/docs/deprecations` for the current vision-capable model and update all 3 nodes together.
- [ ] Telegram webhooks on this Bluehost n8n instance can silently go stale (Flow A went 24+ hours with zero executions on 2026-09-16/17) with no alerting — worth checking periodically or adding a heartbeat/monitor if this recurs.
- [ ] **n8n "Retry" replays the original execution's workflow snapshot on this instance**, even when "Retry with currently saved workflow" is chosen. On 2026-09-17 three consecutive retries reproduced a bug that had already been fixed and published, which sent the debugging down a false path. Verify fixes with a fresh trigger or a scratch workflow, never with retry.
- [ ] **A node can serve a stale compiled version after heavy publish/unpublish cycling.** Symptom: the saved config is provably correct (confirmed by exporting the workflow JSON) but execution behaves as if it were the old config. Remedy that worked: change the parameter to something simple, re-save, and publish fresh so the engine recompiles.
- [ ] **Groq on-demand limit is 1000 output tokens per minute for this account.** Any node with `max_tokens` above that returns `Request too large ... Limit 1000`. Flow A's `Groq Video Tag` is now at 800. Check this before raising `max_tokens` anywhere.
- [ ] **HTTP Request JSON bodies must be in Expression mode, not Fixed**, or `{{ ... }}` is sent as literal text. This silently produced `unsupported protocol` from Groq. Worth checking on any HTTP node whose body contains an expression.
- [ ] **A bulk model-name migration missed a node because it wasn't grouped with the others.** On 17 Sep, 3 Groq *vision* nodes were switched qwen3.6→qwen3.8 together; `Generate SEO Caption` (a text-only Groq call in Flow B) was on the same deprecated model but wasn't part of that group, so it was missed and broke every Flow B run for ~35 hours before being caught (19 Sep). When migrating a model/credential, grep for *every* node using it, not just the ones doing the same kind of call.
