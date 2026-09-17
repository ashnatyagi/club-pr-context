# 04 — Workflows (n8n flows)

> One section per n8n workflow. Copy the template block for each new flow.
> This is the file an agent reads when it's about to build or fix a specific flow.

## The 5 Club PR flows (the whole pipeline)
All flows are named `Club PR - Flow [Letter] - [Purpose]` and are **Published** on the n8n instance. They form one pipeline connected by a single Google Sheet ("Club PR - Asset Bank & Queue"):

```
Telegram (you send image/video)
      │
      ▼
[Flow A] Intake & Tagging ──► writes tagged rows to Sheet "Sheet1"
      │
      ▼
[Flow B] Reactive Reel Generation ──┐  (per-asset reels, event-driven)
[Flow C] Evergreen Listicle Gen ────┤──► both APPEND to Sheet "Posting Queue"
      │                              │
      ▼                              ▼
[Flow D] Posting Queue Publisher ──► publishes to Instagram (Graph API)
```
Plus a helper: **Setup - Upload BG Music Tracks** (one-off utility, not part of the daily loop).

---

## Flow B — Reactive Reel Generation
- **n8n workflow ID / link:** `FwhZhrEOxoFcbqen` — `https://fwe.gsm.mybluehost.me/workflow/FwhZhrEOxoFcbqen`
- **Status:** **Published/live** (confirmed 2026-09-17 — was shown as unpublished/draft on 2026-09-16, has since been published).
- **Purpose:** Turns a tagged club/event asset (image or video) sitting in the Google Sheet into a posting-ready Instagram reel: builds the video, runs an AI quality check, writes an SEO caption, and queues it for posting — or routes it to a human on Telegram if it needs review.
- **Trigger:** Schedule Trigger (runs periodically — confirm exact interval in the node).
- **Inputs it needs:** Rows in the Google Sheet ("Sheet1") with `status = 'tagged'` and `fast_track = 'Y'`, or `status = 'needs_review'`. Each row carries: asset_id, source_type, club_name, event_name, event_date, area, vibe_tag, overlay_zone, fast_track, confidence, cloudinary_public_id (implied), telegram_caption, status, times_used.
- **Steps (high level):**
  1. `Schedule Trigger` → `Get row(s) in sheet` (Google Sheets: read Sheet1) → `Filter` (keep only tagged+fast-tracked or needs-review rows).
  2. `Is Image?` (If node) branches on asset type.
  3. **Image branch:** `Build Zoompan Video URL` (Code: constructs a Cloudinary `e_zoompan` URL with a text overlay of club_name + event_name, falling back to "Tonight"; the `l_text:` layer includes `w_iw_mul_0.8,c_fit` as of 2026-09-17 so long names wrap within 80% of the image width instead of running off the frame) → `Render & Cache Video` (Cloudinary HTTP call, cloud `pemiahac`) → `Restore Fields After Render` (Code: re-attach original row data) → `Groq Vision QC Check` (HTTP POST to Groq's vision chat-completions API, judges legibility/cropping/contrast — it only judges the overlay text itself, not the underlying poster image Ashna already picked) → `Parse QC Result` → `QC Pass?` (If).
     - **True:** `Generate SEO Caption` → `Parse Caption Result` → `Shape Posting Queue Row` → `Append to Posting Queue` → `Mark as Posted` (update sheet).
     - **False:** `Notify Manual Review` (Telegram) → `Mark as Review` (update sheet).
  4. **Non-image branch:** `Is Video For Posting?` (If).
     - **True:** `Notify Non-Image Asset` (Telegram) → `Mark Non-Image Reviewed` (update sheet).
     - **False:** `Prepare Video For Posting` (Code) → feeds back into the notify/posting path.
- **Outputs / where results go:** Passing image reels land in the **"Posting Queue" tab** of the "Club PR - Asset Bank & Queue" Google Sheet (row shape from `Shape Posting Queue Row`: asset_id, club_name, event_name, event_date, area, source_type, final_media_url, media_type, qc_status, qc_reason, caption, hashtags, status='queued', created_at, posted_at). The source row's `status`/`times_used` columns on "Sheet1" are updated throughout; failures/reviews generate a Telegram message to a human (chat routed per club via `asset_id.split('_')[0]`).
- **Depends on:** Google Sheets credential ("Google Sheets account"), Cloudinary account (`pemiahac`), Groq API credential ("Club PR - Groq API", model `qwen/qwen3.8-27b` as of 2026-09-17 — used for both QC and caption generation), Telegram credential ("Club PR - Telegram Bot"), and Flow A which sets `status`/`fast_track`/`vibe_tag` on new "Sheet1" rows.
- **Known issues:** Fixed 2026-09-17 — text overlay was cropping long club+event names, causing false QC fails (see changelog). None currently open.
- **Open questions:** What flow/process sets a row's initial `status`/`vibe_tag`/`overlay_zone` (Flow A)? What reads the "Posting Queue" tab to actually publish to Instagram/social? What is the Schedule Trigger's exact interval? Is `asset_id`'s prefix always a stable club identifier used consistently across flows?

---

## Flow A — Intake & Tagging
- **n8n link:** open from the workflows list ("Club PR - Flow A - Intake & Tagging"). **Status:** Published.
- **Purpose:** The front door. Takes raw content Ashna sends into Telegram, uses AI to auto-tag it (club, event, vibe, etc.), stores the media on Cloudinary, and writes a fully-tagged row into the sheet so the generation flows can act on it.
- **Trigger:** Telegram Trigger (fires when a message/file is sent to the bot).
- **Steps (high level):**
  1. `Telegram Trigger` → `Get a file` (download the sent image/video).
  2. `If` (is it an image? — checks `$binary.data.mimeType.startsWith('image/')`).
     - **Image:** `Edit Image` (resize) → Code → `HTTP Request` to Groq (vision auto-tagging) → Code.
     - **Non-image:** `Code in JavaScript3` (sets `source_type` = `'video'` / `'document'` / `'other'` from the Telegram message) → `Upload an asset from file data` (Cloudinary) → `Code in JavaScript2` → **`Is Video For AI Tag?`** (If node, checks `source_type === 'video'`).
       - **True:** `Build Thumbnail URL` → `Groq Video Tag` (HTTP to Groq on the Cloudinary thumbnail frame) → `Parse Video Tag Result`.
       - **False:** row is appended with blank AI tags and `status = 'needs_review'`.
  3. `Upload an asset from file data` → Cloudinary (gets the `cloudinary_public_id`) — image branch does this separately from the video branch's own upload step above.
  4. `Append row in sheet` → writes a new row to **"Sheet1"** of "Club PR - Asset Bank & Queue" with all the AI tags + status.
- **Outputs:** A new tagged row in "Sheet1" (the input Flow B reads).
- **Depends on:** Telegram credential, Groq ("Club PR - Groq API", model `qwen/qwen3.8-27b` as of 2026-09-17), Cloudinary, Google Sheets.
- **Known issues:**
  - Fixed 2026-09-17 — Telegram webhook can go stale silently (see `09-open-questions.md` known risks); Groq model access loss (see changelog).
  - **Open as of 2026-09-17:** `Is Video For AI Tag?` was sending real videos (confirmed `source_type: "video"` in the input) to its false branch, skipping `Groq Video Tag` entirely and leaving the row blank/`needs_review`. A defensive fix is live but not yet confirmed with a passing live video — see `09-open-questions.md`.

---

## Flow C — Evergreen Listicle Generation
- **n8n link:** "Club PR - Flow C - Evergreen Listicle Generation". **Status:** Published.
- **Purpose:** Produces **evergreen** posts (e.g. listicle-style "round-ups" of multiple assets) rather than single event-reactive reels — the always-relevant content pillar of the goal.
- **Trigger:** Schedule Trigger.
- **Steps (high level):**
  1. `Schedule Trigger` → `Get row(s) in sheet` → `Filter` (keep eligible assets).
  2. `Select Listicle Assets` (Code — picks a set of assets to combine).
  3. `Generate Listicle Caption` (HTTP to Groq).
  4. `Shape Posting Queue Row` → `Append to Posting Queue`; also `Build time…/Update row in sheet` to record usage.
- **Outputs:** New row(s) in the **"Posting Queue"** tab, ready for Flow D.
- **Depends on:** Google Sheets, Groq ("Club PR - Groq API").
- **Known issues:** None recorded yet — add here as they come up.

---

## Flow D — Posting Queue Publisher
- **n8n link:** "Club PR - Flow D - Posting Queue Publisher". **Status:** live and **running automatically** — per Ashna, posting is fully automated; nobody checks or triggers it by hand.
- **Purpose:** Reads the Posting Queue and publishes finished content to Instagram via the **Instagram Graph API**, handling both single reels and multi-image carousels, then records the result. Runs on its own with no human step.
- **Trigger:** Schedule Trigger.
- **Steps (high level):**
  1. `Schedule Trigger` → `Get Posting Queue` (read sheet) → `Filter Queued` (status = queued) → `Pick Oldest Queued` (Code).
  2. `Is Video?` (If) branches on media type.
     - **Reel (video):** `Create Reels Container` (Instagram Graph API POST `graph.instagram.com/v24.0/{ig_account_id}/media`) → `Wait Before Status Check` → `Check Reels Status` → `Reel Ready?`
       - **Ready:** `Publish Reel` → `Mark as Posted`.
       - **Not ready / fail:** `Mark as Post Failed` → `Notify Post Failed` (Telegram) → `Shape Failed Update`.
     - **Carousel (multi-image):** `Split Media URLs` → `Create Carousel Child` (+ `Warm Cloudinary Cache`) → `Aggregate Child IDs` → `Create Carousel Container` → `Publish Carousel` → `Shape Posted Update`.
- **Outputs:** Live Instagram post; Posting Queue row marked `posted` (with `posted_at`) or `failed`.
- **Depends on:** Google Sheets, **Instagram Graph API (credential "Club PR - Instagram Access Token", Bearer)**, Cloudinary (media URLs), Telegram (failure alerts). Each queue row carries its own `ig_account_id`, so **one flow publishes to multiple Instagram pages**.
- **Known issues:** None recorded yet — watch the Instagram token expiry / Graph API version (v24.0), the most likely silent break point.

---

## Setup — Upload BG Music Tracks
- **n8n link:** "Club PR - Setup - Upload BG Music Tracks". **Status:** utility (not "Published" as a live loop).
- **Purpose:** One-off / occasional helper to upload background music tracks (used to soundtrack reels). Run manually when new tracks are needed; not part of the daily pipeline.
- **Known issues:** None recorded yet.

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
