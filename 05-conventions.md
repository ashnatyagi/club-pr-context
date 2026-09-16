# 05 — Conventions & Standards

> Read before creating or renaming anything, so all agents stay consistent.

## Naming
- **Workflows:** `Club PR - Flow [Letter] - [Purpose]` — confirmed in use (e.g. "Club PR - Flow B - Reactive Reel Generation").
- **Nodes inside a flow:** Plain-English, verb/question style: action nodes are verb-first ("Build Zoompan Video URL", "Notify Manual Review"), branch nodes are phrased as yes/no questions ("Is Image?", "QC Pass?", "Is Video For Posting?").
- **Credentials:** Descriptive, project-prefixed where it's an external API — e.g. "Club PR - Groq API"; plain service name for generic ones — e.g. "Google Sheets account".
- **Sheet columns:** lower_snake_case (e.g. `fast_track`, `cloudinary_public_id`, `overlay_zone`).
- **Files in this KB:** `NN-topic.md` (numbered, lowercase, dashes)

## Do
- **Test changes in a duplicated/draft flow** before touching a live one — don't edit a Published flow's real behaviour directly.
- **Reuse existing `status` values and column names** (see the sheet contract below) rather than inventing parallel ones.
- **Log every change** in `07-changelog.md` and update the relevant KB file.
- **Fetch only the 1–2 relevant KB files** into an agent, not the whole thing (keeps tokens low).

## Don't
- Don't put secrets/keys/tokens in these docs (only ever name WHERE they live).
- Don't rename or delete a Google Sheet column without checking every flow that uses it.
- Don't add a manual "Ashna approves each post" step — posting is automated by design (see `10-operating-principles.md`).

## The Google Sheet is a shared contract
Every flow reads/writes the one "Club PR - Asset Bank & Queue" sheet, so its columns are a contract shared across Flows A–D:
- **Don't rename or delete a column** without checking every flow that uses it — a change can break several flows at once.
- Keep the two tabs distinct: **"Sheet1"** = intake/asset bank (Flow A writes, Flow B/C read), **"Posting Queue"** = output (Flow B/C write, Flow D reads).
- `status` values seen in use: intake side `tagged` / `needs_review`; queue side `queued` / `posted` / `failed`. Reuse these, don't invent parallel ones.

## Secrets handling
- All credentials live in **n8n Credentials** (e.g. "Club PR - Groq API", "Club PR - Telegram Bot", "Club PR - Instagram Access Token", "Google Sheets account"), never in docs or chat. This KB only ever names WHERE a credential lives, never its value.
