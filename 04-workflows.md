# 04 — Workflows (n8n flows)

> One section per n8n workflow. Copy the template block for each new flow.
> This is the file an agent reads when it's about to build or fix a specific flow.

---

## Flow B — Reactive Reel Generation
- **n8n workflow ID / link:** `FwhZhrEOxoFcbqen` — `https://fwe.gsm.mybluehost.me/workflow/FwhZhrEOxoFcbqen`
- **Status:** shown as unpublished/draft in n8n as of 2026-09-16 (Publish button visible, not yet toggled live) — confirm actual live status.
- **Purpose:** Turns a tagged club/event asset (image or video) sitting in the Google Sheet into a posting-ready Instagram reel: builds the video, runs an AI quality check, writes an SEO caption, and queues it for posting — or routes it to a human on Telegram if it needs review.
- **Trigger:** Schedule Trigger (runs periodically — confirm exact interval in the node).
- **Inputs it needs:** Rows in the Google Sheet ("Sheet1") with `status = 'tagged'` and `fast_track = 'Y'`, or `status = 'needs_review'`. Each row carries: asset_id, source_type, club_name, event_name, event_date, area, vibe_tag, overlay_zone, fast_track, confidence, cloudinary_public_id (implied), telegram_caption, status, times_used.
- **Steps (high level):**
  1. `Schedule Trigger` → `Get row(s) in sheet` (Google Sheets: read Sheet1) → `Filter` (keep only tagged+fast-tracked or needs-review rows).
  2. `Is Image?` (If node) branches on asset type.
  3. **Image branch:** `Build Zoompan Video URL` (Code: constructs a Cloudinary `e_zoompan` URL with a text overlay of club_name + event_name, falling back to "Tonight") → `Render & Cache Video` (Cloudinary HTTP call, cloud `pemiahac`) → `Restore Fields After Render` (Code: re-attach original row data) → `Groq Vision QC Check` (HTTP POST to Groq's vision chat-completions API, judges legibility/cropping/contrast) → `Parse QC Result` → `QC Pass?` (If).
     - **True:** `Generate SEO Caption` → `Parse Caption Result` → `Shape Posting Queue Row` → `Append to Posting Queue` → `Mark as Posted` (update sheet).
     - **False:** `Notify Manual Review` (Telegram) → `Mark as Review` (update sheet).
  4. **Non-image branch:** `Is Video For Posting?` (If).
     - **True:** `Notify Non-Image Asset` (Telegram) → `Mark Non-Image Reviewed` (update sheet).
     - **False:** `Prepare Video For Posting` (Code) → feeds back into the notify/posting path.
- **Outputs / where results go:** Passing image reels land in the **"Posting Queue" tab** of the "Club PR - Asset Bank & Queue" Google Sheet (row shape from `Shape Posting Queue Row`: asset_id, club_name, event_name, event_date, area, source_type, final_media_url, media_type, qc_status, qc_reason, caption, hashtags, status='queued', created_at, posted_at). The source row's `status`/`times_used` columns on "Sheet1" are updated throughout; failures/reviews generate a Telegram message to a human (chat routed per club via `asset_id.split('_')[0]`).
- **Depends on:** Google Sheets credential ("Google Sheets account"), Cloudinary account (`pemiahac`), Groq API credential ("Club PR - Groq API" — used for both QC and caption generation), Telegram credential ("Club PR - Telegram Bot"), and whatever upstream flow sets `status`/`fast_track`/`vibe_tag` on new "Sheet1" rows (likely "Flow A" — name/ID unconfirmed).
- **Known issues:** [ FILL IN — none captured yet; add here as they come up ]
- **Open questions:** What flow/process sets a row's initial `status`/`vibe_tag`/`overlay_zone` (Flow A)? What reads the "Posting Queue" tab to actually publish to Instagram/social? What is the Schedule Trigger's exact interval? Is `asset_id`'s prefix always a stable club identifier used consistently across flows?

---

## Flow A — [ name ]
- **n8n workflow ID / link:** [ FILL IN ]
- **Status:** [ FILL IN ]
- **Purpose:** [ FILL IN ]
- **Trigger:** [ FILL IN ]
- **Inputs it needs:** [ FILL IN ]
- **Steps (high level):**
  1. [ FILL IN ]
- **Outputs / where results go:** [ FILL IN ]
- **Depends on:** [ FILL IN ]
- **Known issues:** [ FILL IN ]

---

<!-- TEMPLATE — copy for each new flow
## Flow [X] — [ name ]
- **n8n workflow ID / link:**
- **Status:**
- **Purpose:**
- **Trigger:**
- **Inputs it needs:**
- **Steps (high level):**
  1.
- **Outputs / where results go:**
- **Depends on:**
- **Known issues:**
-->
