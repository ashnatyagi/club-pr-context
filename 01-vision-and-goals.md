# 01 — Vision & Goals

> The "why" behind the project. Everything else — every flow, every agent, every person — serves this.

## North Star (long-term goal)
Build and run **multiple keyword-rich Instagram pages** that publish daily club-related content (stories, reels, and evergreen posts). People discover the pages, engage, and reach out — those inquiries become **leads**. Each lead (a club / PR) is handed over **free at first to prove value, then converted into a paying client.** In short: the Instagram pages are a lead-generation engine for a club-PR business, and the automation exists to run that engine at scale with almost no manual work.

## The funnel (how the goal actually works)
1. **Publish** — keyword-rich IG pages post daily club stories, reels, and evergreen content.
2. **Discover & engage** — users find the pages (via keywords/hashtags) and watch/interact.
3. **Contact** — interested users reach out.
4. **Lead** — that contact becomes a lead for a club / PR.
5. **Free trial** — the lead is given to a club for free first, to prove the value.
6. **Monetize** — once value is shown, start charging the club. 💰

## Where we are today (Sept 2026)
- Content pipeline is **semi-automated**: images/videos are sent into a **Telegram channel**, then edited and turned into posts.
- The **n8n flows** (see `04-workflows.md`) handle tagging + reel generation + QC + queueing.
- **Posting is still manual** — Ashna posts the finished content herself for now.

## Objectives (the road to full automation)
The end state is **~100% automation** of the publish side. Concretely, over time the system should also handle:
- [ ] **Post-timing analysis** — figure out and schedule the best time to post.
- [ ] **Hashtag strategy** — pick/rotate hashtags and track which ones actually work.
- [ ] **Performance feedback loop** — measure what content is working vs. not, and feed that back into what gets made.
- [ ] **Fully automated posting** — remove the manual posting step so content goes out end-to-end without Ashna.

## Success metrics
- Number of keyword-rich IG pages live and posting daily.
- Inbound contacts → leads generated per page.
- Leads → free trials → **paying clients** (the real bottom line).
- % of the publish pipeline that runs without manual work (moving toward ~100%).

## Non-goals (to prevent scope creep)
- Not building content tools from scratch where an existing service does the job (Cloudinary for video, Groq for AI, etc.).
- Not chasing full automation at the cost of quality — the human/AI QC gate stays until automated quality is trusted.
- [ FILL IN — add any others as they come up ]
