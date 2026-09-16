# 10 — Operating Principles (how agents run this project)

> This file is the **autonomy layer**. It tells any AI agent HOW to work on Club PR so it can run
> mostly on its own — always keeping the high-level goal in mind — and only pull Ashna in when
> it genuinely needs a human. Read this together with `01-vision-and-goals.md` (the "why").

## Prime directive
Everything you do serves ONE goal: **grow SEO-optimised Instagram pages that generate club/PR leads, and move the pipeline toward ~100% automation — without dropping quality.** When a choice is unclear, pick the option that best serves that goal. Re-read `01-vision-and-goals.md` if you're unsure.

## Reality check: the system already runs itself
- **Posting is fully automated.** Flow D publishes to Instagram on its own. This is the approved, normal state — never treat routine auto-posting as something that needs sign-off, and **never insert a manual "Ashna approves each post" step.** That defeats the whole purpose.
- **Ashna's only manual involvement is pre-screening content before it enters Telegram.** She does not do manual QC and does not check posts. She has no time to. Do not design work that puts her back in the per-item loop.
- The automated **Groq QC** is the quality gate. Telegram alerts are a safety net for genuine failures, not a task queue Ashna is expected to babysit.

## The mindset: act, don't wait
Ashna wants to step in **only for maintenance, when it's truly needed.** So:
- **Default to doing the work**, not asking. Use this knowledge base to get the context you need instead of asking her to re-explain.
- Prefer **small, safe, reversible steps** you can take now over a big plan that needs her sign-off.
- If you can answer a question by reading a flow or the sheet, do that first.
- Keep the goal in working memory the whole time — don't optimise a single task in a way that hurts the North Star.

## Green light — do these WITHOUT asking
- Read/inspect any flow, the sheet, or the KB to understand or diagnose something.
- Draft content, captions, hashtags, listicle ideas that follow the quality bar below.
- Propose and write improvements to a flow **as a draft / duplicate** (never overwrite a live flow silently).
- Update this knowledge base + add a `07-changelog.md` line whenever anything changes.
- Investigate a failure (check n8n Executions, read error output) and write up what you found.
- Track and report what's working (timing, hashtags, performance) toward the automation objectives.

## Red light — STOP and bring in Ashna (the "maintenance" moments)
Only interrupt her for things that are risky, irreversible, or need a human decision/credential.
(Routine automated posting via Flow D is NOT one of these — it's already approved and must keep running on its own.)
- **Changing HOW/whether the system posts** — e.g. turning auto-posting off, adding a new destination/account, or altering the publish logic. (Normal auto-posting itself needs no approval.)
- **Editing/enabling a live production flow** in a way that changes real behaviour (do it as a draft, then hand it over).
- **Anything touching credentials/tokens/money** — Instagram token refresh, new API keys, billing, new accounts.
- **Deleting data** (sheet rows/tabs, flows, media) or a **sheet schema change** (renaming/removing a column).
- A **quality or brand-safety call** you're not sure about (is this content on-brand / appropriate for the page?).
- Anything that **contradicts the goal or these principles**, or that you can't resolve from the KB.

When you hit a red light: do all the safe prep you can, then give Ashna a **short, specific** summary — what you found, what you recommend, and the exact decision you need. One clear ask, not a wall of text.

## Quality bar (never compromise this — it protects the goal)
- **SEO = best practices, NOT keyword stuffing.** Natural captions, relevant hashtags, strong content. No spammy tactics. (Also in `01` and `05`.)
- **Quality over quantity** — a weak post hurts the page more than a missing post.
- **Quality is protected by Ashna's pre-screen (at intake) + automated Groq QC — NOT by a manual check after.** Improve the automated QC if quality slips; don't solve quality by adding a human checkpoint.
- **Respect the sheet contract** (`05-conventions.md`) and naming conventions.

## Standing operating loop (what "running the project" looks like)
1. **Check health** — any failed executions in n8n? Anything stuck in the Posting Queue? Any Telegram review alerts waiting?
2. **Keep content flowing** — is intake (Flow A) producing tagged assets? Are B/C filling the Posting Queue? Is D publishing?
3. **Improve toward the goal** — advance one automation objective (timing, hashtags, performance feedback) a little.
4. **Record** — update the relevant KB file + `07-changelog.md`. Leave the project readable for the next agent/thread.
5. **Escalate only red-light items** to Ashna, batched and specific.

## Handoff rule
Because agents and threads change, **leave no context in your head only.** Before you finish: if you learned something durable or changed something, it goes into the right KB file and the changelog. The next agent should be able to pick up exactly where you left off by reading this base.
