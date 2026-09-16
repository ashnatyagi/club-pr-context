# 09 — Open Questions & Backlog

> Everything not yet decided or done. Agents look here for "what's next" and known risks.
> Move items into a decision (`06`) or changelog (`07`) once resolved.

## To decide
- [ ] What is "Flow A" (or whatever sets `status`/`fast_track`/`vibe_tag`/`overlay_zone` on a sheet row before Flow B can act)? Document it in `04-workflows.md` once confirmed.
- [ ] What actually reads the "Posting Queue" and publishes to Instagram/social platforms — is that a Flow C, a manual step, or something else?
- [x] Which AI model/provider powers "Generate SEO Caption"? → Groq API (same "Club PR - Groq API" credential as the vision QC step).
- [ ] Exact Schedule Trigger interval for Flow B.
- [ ] Is Flow B currently live/published, or still in draft? (n8n showed an un-toggled "Publish" button as of 2026-09-16.)

## To build / TODO
- [ ] Fill remaining `[ FILL IN ]` blanks across `01-vision-and-goals.md` (long-term goal, objectives, success metrics).
- [ ] Document "Flow A" (or whichever flow tags incoming assets) once confirmed, using the template in `04-workflows.md`.
- [ ] Set up the GitHub token / access so n8n's HTTP nodes and other AI agents can actually pull this context automatically.

## Known risks
- [ ] Single Bluehost VPS = single point of failure for all n8n flows; confirm backup/restore plan.
- [ ] Manual review relies on someone actually watching Telegram — no fallback if it's missed.
