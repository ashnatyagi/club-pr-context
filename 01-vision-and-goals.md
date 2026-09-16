# 01 — Vision & Goals

> The "why" behind the project. Everything else — every flow, every agent, every person — serves this.

## North Star (long-term goal)
Build and run **multiple SEO-optimised Instagram pages** that publish daily club-related content (stories, reels, and evergreen posts). People discover the pages, engage, and reach out — those inquiries become **leads**. Leads are handed to **PR people Ashna personally knows, free for some time to build the relationship, then converted into paying clients.** In short: the Instagram pages are a lead-generation engine for a club-PR business, and the automation exists to run that engine at scale with almost no manual work.

> ⚠️ **SEO = best practices, NOT keyword stuffing.** Content and pages are optimised for discovery the right way (clean captions, natural keywords, good hashtags, strong content). No stuffing, no spammy tactics — quality first.

## The funnel (how the goal actually works)
1. **Publish** — SEO-optimised IG pages post daily club stories, reels, and evergreen content (best-practice, no stuffing).
2. **Discover & engage** — users find the pages naturally (search, keywords, hashtags) and watch/interact.
3. **Contact** — interested users reach out.
4. **Lead** — that contact becomes a lead.
5. **Hand off (free at first)** — Ashna passes leads to PR people she personally knows, **free for some time** to build the relationship and prove value.
6. **Monetize** — once value is shown, start charging. 💰

## Where we are today (Sept 2026)
- The pipeline **runs automatically end-to-end**: 4 connected n8n flows (A→B/C→D, see `04-workflows.md`) cover intake+tagging, reel generation, evergreen listicles, and Instagram publishing — **including posting, which is fully automated (Flow D posts to Instagram on its own).**
- **Ashna's ONLY manual step is at the very start:** she reviews/curates images and videos herself, then sends them into the Telegram bot. After that she does **not** check anything — no manual QC, no manual posting. Her time is limited; the system is meant to run without her in the loop.
- The AI (Groq) handles tagging, quality-checking, and captions automatically; Cloudinary builds the media; items queue and publish on their own.
- **The gap to close:** not "turn on auto-posting" (that's already on) — it's the **intelligence layer**: post-timing analysis, hashtag performance, and a content feedback loop, moving quality toward "as good as her pre-screen" without her checking.

## Objectives (the road to full automation)
The end state is **~100% automation** of the publish side, always following SEO/content best practices (never stuffing). Concretely, over time the system should also handle:
- [ ] **Post-timing analysis** — figure out and schedule the best time to post.
- [ ] **Hashtag strategy** — pick/rotate relevant hashtags naturally and track which ones actually work (no spammy hashtag walls).
- [ ] **Performance feedback loop** — measure what content is working vs. not, and feed that back into what gets made.
- [ ] **Fully automated posting** — remove the manual posting step so content goes out end-to-end without Ashna.

## Success metrics
- Number of SEO-optimised IG pages live and posting daily.
- Inbound contacts → leads generated per page.
- Leads → free hand-offs to known PR people → **paying clients** (the real bottom line).
- % of the publish pipeline that runs without manual work (moving toward ~100%).

## Non-goals (to prevent scope creep)
- **No keyword stuffing or spammy SEO/hashtag tactics** — discovery is earned through best practices and good content, not tricks.
- Not building content tools from scratch where an existing service does the job (Cloudinary for video, Groq for AI, etc.).
- Not chasing full automation at the cost of quality — automated QC (and Ashna's pre-screen at intake) protects quality; no manual per-post checking.
- Not putting Ashna back in the per-post loop — her time is limited by design.
