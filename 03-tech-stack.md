# 03 — Tech Stack (tools & how we use them)

> Every tool in the project. If an agent needs to know "what do we use for X and how", it's here.
> ⚠️ Never write real passwords, API keys, or tokens in this file. Only say WHERE a credential lives (e.g. "in n8n Credentials as 'IG-main'").

| Tool | What it's for | How we use it | Where it lives | Credential location |
|------|---------------|---------------|----------------|--------------------|
| n8n | Workflow automation engine | Hosts all the flows (Flow A tagging, Flow B reel generation, etc.) | Self-hosted at `fwe.gsm.mybluehost.me` on a Bluehost VPS | n8n internal Credentials |
| Bluehost VPS | Server / hosting | Runs the n8n instance | Bluehost account | — |
| Google Sheets — "Club PR - Asset Bank & Queue" | Central data store / status queue for the whole project | One Google Sheets **document** with (at least) two tabs: **"Sheet1"** — the intake/asset-bank tab (asset_id, source_type, club_name, event_name, event_date, area, vibe_tag, overlay_zone, fast_track, confidence, parse_error, received_at, status, times_used, telegram_caption, cloudinary_public_id, image_url, etc.) and **"Posting Queue"** — the output tab (asset_id, club_name, event_name, event_date, area, source_type, final_media_url, media_type, qc_status, qc_reason, caption, hashtags, status, created_at, posted_at) | Read via "Get row(s) in sheet", appended via "Append to Posting Queue", updated via "Mark as..." nodes | n8n credential "Google Sheets account" |
| Cloudinary (cloud name `pemiahac`) | Image hosting + on-the-fly video generation | A Code node builds a Cloudinary transformation URL (`e_zoompan` Ken Burns effect, text overlay, 8s @ 24fps) from `cloudinary_public_id`; an HTTP node renders/caches it | Cloudinary account | n8n credential used in "Render & Cache Video" node |
| Groq API | AI vision QC **and** SEO caption generation | HTTP POST to `https://api.groq.com/openai/v1/chat/completions`. Used twice: (1) "Groq Vision QC Check" — judges the rendered reel for text legibility, cropping, color/contrast; (2) "Generate SEO Caption" — writes the caption/hashtags for a passing reel | Groq | n8n credential "Club PR - Groq API" (Bearer auth) — same credential, both nodes |
| Telegram (Bot API) | Human-in-the-loop notifications | "Notify Manual Review" and "Notify Non-Image Asset" send a message when AI QC fails or an asset needs a human look. Chat is routed **per club**: Chat ID = `{{ $json.asset_id.split('_')[0] }}` — i.e. asset_id is prefixed with a club identifier that doubles as (or maps to) its Telegram chat. Message includes club name, event name, QC failure reason, and both the rendered + original media URLs | Telegram bot | n8n credential "Club PR - Telegram Bot" |
| "Posting Queue" | Holds reels that passed QC, ready to publish | It's the **"Posting Queue" tab of the same "Club PR - Asset Bank & Queue" Google Sheet** (not a separate system) — confirmed via the "Append to Posting Queue" node | Google Sheets | same "Google Sheets account" credential |
| Publishing to social platforms (Instagram etc.) | Actually posting the reel | [ FILL IN — appears to be a separate flow/manual step reading the "Posting Queue" tab; confirm which flow ] | [ FILL IN ] | [ FILL IN ] |

## Notes / gotchas
- The workflow naming convention confirmed in use: **"Club PR - Flow [Letter] - [Purpose]"** (e.g. "Club PR - Flow B - Reactive Reel Generation").
- Rows only get picked up by Flow B when `status = 'tagged' AND fast_track = 'Y'`, or `status = 'needs_review'` — something upstream (likely "Flow A") must set `status`/`fast_track`/`vibe_tag`/`overlay_zone` before this flow can act on a row.
- QC is a two-stage gate: automated (Groq Vision) first, human (Telegram) second — a failed AI QC does not block the item, it routes to a person instead of silently dying.
- [ FILL IN — add VPS-specific gotchas, e.g. restart schedule, token expiries, rate limits ]
