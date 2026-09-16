# 03 — Tech Stack (tools & how we use them)

> Every tool in the project. If an agent needs to know "what do we use for X and how", it's here.
> ⚠️ Never write real passwords, API keys, or tokens in this file. Only say WHERE a credential lives (e.g. "in n8n Credentials as 'IG-main'").

| Tool | What it's for | How we use it | Where it lives | Credential location |
|------|---------------|---------------|----------------|--------------------|
| n8n | Workflow automation engine | Hosts all the flows (Flow A tagging, Flow B reel generation, etc.) | Self-hosted at `fwe.gsm.mybluehost.me` on a Bluehost VPS | n8n internal Credentials |
| Bluehost VPS | Server / hosting | Runs the n8n instance | Bluehost account | — |
| Google Sheets — "Club PR - Asset Bank & Queue" | Central data store / status queue for the whole project | One Google Sheets **document** with (at least) two tabs: **"Sheet1"** — the intake/asset-bank tab (asset_id, source_type, club_name, event_name, event_date, area, vibe_tag, overlay_zone, fast_track, confidence, parse_error, received_at, status, times_used, telegram_caption, cloudinary_public_id, image_url, etc.) and **"Posting Queue"** — the output tab (asset_id, club_name, event_name, event_date, area, source_type, final_media_url, media_type, qc_status, qc_reason, caption, hashtags, status, created_at, posted_at) | Read via "Get row(s) in sheet", appended via "Append to Posting Queue", updated via "Mark as..." nodes | n8n credential "Google Sheets account" |
| Cloudinary (cloud name `pemiahac`) | Image hosting + on-the-fly video generation | A Code node builds a Cloudinary transformation URL (`e_zoompan` Ken Burns effect, text overlay, 8s @ 24fps) from `cloudinary_public_id`; an HTTP node renders/caches it | Cloudinary account | n8n credential used in "Render & Cache Video" node |
| Groq API | The AI brain — tagging, QC, captions | HTTP POST to `https://api.groq.com/openai/v1/chat/completions`. Used across the pipeline: (1) Flow A — auto-tag incoming images/videos; (2) Flow B "Groq Vision QC Check" — judge a rendered reel for legibility/cropping/contrast; (3) Flow B "Generate SEO Caption" & Flow C "Generate Listicle Caption" — write captions/hashtags | Groq | n8n credential "Club PR - Groq API" (Bearer auth) — same credential everywhere |
| Telegram (Bot API) | **Input** + human alerts | Two roles: (1) **Input** — Flow A's trigger is a Telegram message; Ashna sends raw images/videos into the bot to feed the pipeline. (2) **Alerts** — Flows B & D send a message when QC fails, an asset needs a human look, or a post fails. Chat routed **per club**: Chat ID = `{{ $json.asset_id.split('_')[0] }}` (asset_id is prefixed with a club identifier) | Telegram bot | n8n credential "Club PR - Telegram Bot" |
| Instagram Graph API | **Publishing** to Instagram | Flow D posts to `https://graph.instagram.com/v24.0/{ig_account_id}/media` — creates a media container (reel) or child+parent containers (carousel), polls status, then publishes. `ig_account_id` comes from each queue row, so one flow serves **multiple IG pages** | Instagram / Meta | n8n credential "Club PR - Instagram Access Token" (Bearer auth) |
| "Posting Queue" | Holds finished content ready to publish | The **"Posting Queue" tab of the same "Club PR - Asset Bank & Queue" Google Sheet** (not a separate system). Flows B & C append; Flow D consumes | Google Sheets | same "Google Sheets account" credential |

## Notes / gotchas
- The workflow naming convention confirmed in use: **"Club PR - Flow [Letter] - [Purpose]"** (e.g. "Club PR - Flow B - Reactive Reel Generation").
- Rows get picked up by Flow B when `status = 'tagged' AND fast_track = 'Y'`, or `status = 'needs_review'`. **Flow A** is what sets `status`/`fast_track`/`vibe_tag`/`overlay_zone` on new rows.
- QC is a two-stage gate: automated (Groq Vision) first, human (Telegram) second — a failed AI QC does not block the item, it routes to a person instead of silently dying.
- The **Instagram access token expires** and Meta Graph API versions age out (currently v24.0) — these are the most likely things to silently break Flow D. Watch for it.
- One Google Sheet is the whole database — a schema change (renaming/removing a column) can break multiple flows at once. Treat sheet columns as a shared contract (see `05-conventions.md`).
- n8n Overview showed ~0.6% failure rate over ~12.7k executions as of 2026-09-16 — healthy, but check the Executions tab when something looks off.
- [ FILL IN — add VPS-specific gotchas, e.g. restart schedule, backup plan, rate limits ]
