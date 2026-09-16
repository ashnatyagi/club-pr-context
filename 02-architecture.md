# 02 — Architecture (how it all fits together)

> The big picture, in plain words. Update this whenever the shape of the system changes.

## One-paragraph summary
Club-related assets (images/videos tied to clubs, events, and vibes) land in a Google Sheet — either tagged upstream (by another flow / a Telegram-fed tagging process, "source_type"/"vibe_tag"/"overlay_zone" columns suggest an upstream classification step) or manually. **Flow B ("Reactive Reel Generation")** runs on a schedule, pulls rows that are ready ("tagged" + fast-tracked, or "needs_review"), and for image assets: builds a Ken-Burns-style ("zoompan") video from the still image via Cloudinary, runs an AI vision QC pass (Groq) to check the text overlay/quality, generates an SEO caption, and — if QC passes — pushes the row into a "Posting Queue" and marks it posted; if QC fails, it notifies a human on Telegram for manual review. Non-image (video) assets go through a separate lighter path (notify + mark reviewed / prepare for posting).

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
- **Google Sheets ("Club PR - Asset Bank & Queue")** — the central data store / queue for assets and their status, with an intake tab ("Sheet1") and an output tab ("Posting Queue").
- **Cloudinary** (cloud name `pemiahac`) — image hosting + video generation (zoompan effect) from Cloudinary-hosted images.
- **Groq API** (`api.groq.com`, credential "Club PR - Groq API") — vision-capable LLM used for automated QC of the generated reel.
- **Telegram** — human-in-the-loop notifications (manual review requests, non-image asset alerts), routed to a per-club chat derived from the asset's ID prefix.
- **n8n** itself, self-hosted on the Bluehost VPS — if the VPS goes down, all flows stop.
