# 02 — Architecture (how it all fits together)

> The big picture, in plain words. Update this whenever the shape of the system changes.

## One-paragraph summary
The system is a 4-stage pipeline (Flows A→B/C→D) tied together by one Google Sheet, turning raw content Ashna sends into Telegram into finished Instagram posts across multiple pages — **fully automatically, including the posting step.** The ONLY human touch point is at the very start: Ashna pre-screens/curates the images and videos before sending them into the Telegram bot. After that there is no manual check and no manual posting. **Flow A** ingests + AI-tags content from Telegram and stores it. **Flow B** turns individual tagged assets into event-reactive reels; **Flow C** turns sets of assets into evergreen listicle posts. Both drop finished items into a "Posting Queue". **Flow D** reads that queue and publishes to Instagram via the Graph API. AI (Groq) does the tagging, quality-checking, and caption writing; Telegram is used for human alerts when something needs a look.

## The pipeline at a glance
```
You send content ──► [A] Intake & Tagging ──► Sheet "Sheet1" (tagged assets)
                                                     │
                          ┌──────────────────────────┴───────────────┐
                          ▼                                            ▼
              [B] Reactive Reel Generation              [C] Evergreen Listicle Generation
                          │                                            │
                          └──────────► Sheet "Posting Queue" ◄─────────┘
                                                     │
                                                     ▼
                                   [D] Posting Queue Publisher ──► Instagram (Graph API)
```

## Flow B detail (per-asset reels)
Below is the internals of Flow B specifically (the other flows are in `04-workflows.md`). It runs on a schedule, pulls rows that are ready ("tagged" + fast-tracked, or "needs_review"), and for image assets builds a Ken-Burns-style ("zoompan") video from the still image via Cloudinary, runs an AI vision QC pass (Groq) to check the text overlay/quality, generates an SEO caption, and — if QC passes — pushes the row into the "Posting Queue" and marks it posted; if QC fails, it notifies a human on Telegram for manual review. Non-image (video) assets go through a separate lighter path (notify + mark reviewed / prepare for posting).

## Data flow (end to end)
1. **Trigger:** Schedule Trigger (n8n) — runs Flow B periodically.
2. **Input gathering:** `Get row(s) in sheet` (Google Sheets, "Sheet1") → `Filter` node keeps rows where `status === 'tagged' && fast_track === 'Y'` OR `status === 'needs_review'`.
3. **Branch — Is Image?**
   - **True (image asset):**
     a. `Build Zoompan Video URL` — a Code node builds a Cloudinary video-transformation URL: Ken Burns zoom/pan (`e_zoompan`, 8s, 24fps) over the image, with a text overlay layer (club name + event name, or "Tonight" as fallback).
     b. `Render & Cache Video` — calls Cloudinary (cloud name: `pemiahac`) to render/cache that video.
     c. `Restore Fields After Render` — re-attaches the original sheet-row fields (Code node) since the HTTP call strips them.
     d. `Groq Vision QC Check` — POSTs the rendered video/frame to Groq's vision-capable chat-completions API to judge posting-suitability (text fully legible, not cut off, not overlapping distracting parts, acceptable colors/contrast for a professional Instagram reel). Returns JSON.
     e. `Parse QC Result` → `QC Pass?` (If node) branches on the QC verdict.
        - **Pass:** `Generate SEO Caption` (AI) → `Parse Caption Result` → `Shape Posting Queue Row` → `Append to Posting Queue` → `Mark as Posted` (sheet updated).
        - **Fail:** `Notify Manual Review` (Telegram message to a human) → `Mark as Review` (sheet updated to reflect it needs a human look).
   - **False (not an image, e.g. a video asset): → `Is Video For Posting?`**
     - **True:** `Notify Non-Image Asset` (Telegram) → `Mark Non-Image Reviewed` (sheet updated).
     - **False:** `Prepare Video For Posting` → feeds into the same notify/posting path.
4. **Review / approval:** Automatic AI QC (Groq Vision) is the first gate; failures fall back to manual human review via Telegram.
5. **Publish / schedule:** Passing items are appended to the **"Posting Queue" tab** of the same Google Sheet document ("Club PR - Asset Bank & Queue") rather than posted directly from this flow — actual publishing to social platforms appears to be a separate step/flow reading that tab.
6. **Logging:** Status, review state, and "times_used" are tracked back on the "Sheet1" tab (columns: `status`, `times_used`, `telegram_caption`, etc.); queued items are logged on the "Posting Queue" tab (`status`, `created_at`, `posted_at`).

## Environments
| Environment | Where | Purpose |
|-------------|-------|---------|
| Production | n8n self-hosted, Bluehost VPS (`fwe.gsm.mybluehost.me`) | Live workflows |
| Test / staging | none yet | — |

## Key external dependencies
- **Google Sheets ("Club PR - Asset Bank & Queue")** — the central data store / queue for the whole pipeline, with an intake tab ("Sheet1") and an output tab ("Posting Queue"). This sheet is the "database" every flow reads/writes.
- **Cloudinary** (cloud name `pemiahac`) — media hosting + video generation (zoompan effect) + carousel image hosting.
- **Groq API** (`api.groq.com`, credential "Club PR - Groq API") — the AI brain: used for intake auto-tagging (Flow A), reel QC (Flow B), and caption generation (Flows B & C).
- **Instagram Graph API** (`graph.instagram.com/v24.0`, credential "Club PR - Instagram Access Token") — publishes reels and carousels (Flow D). Each row's `ig_account_id` picks which page it posts to → multi-page support.
- **Telegram** — both an **input** (Flow A trigger: Ashna sends content into the bot) and an **alert channel** (Flows B & D notify a human when something needs review or a post fails), routed to a per-club chat derived from the asset's ID prefix.
- **n8n** itself, self-hosted on the Bluehost VPS — if the VPS goes down, all flows stop.
