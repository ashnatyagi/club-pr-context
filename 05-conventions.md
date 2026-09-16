# 05 — Conventions & Standards

> Read before creating or renaming anything, so all agents stay consistent.

## Naming
- **Workflows:** `Club PR - Flow [Letter] - [Purpose]` — confirmed in use (e.g. "Club PR - Flow B - Reactive Reel Generation").
- **Nodes inside a flow:** Plain-English, verb/question style: action nodes are verb-first ("Build Zoompan Video URL", "Notify Manual Review"), branch nodes are phrased as yes/no questions ("Is Image?", "QC Pass?", "Is Video For Posting?").
- **Credentials:** Descriptive, project-prefixed where it's an external API — e.g. "Club PR - Groq API"; plain service name for generic ones — e.g. "Google Sheets account".
- **Sheet columns:** lower_snake_case (e.g. `fast_track`, `cloudinary_public_id`, `overlay_zone`).
- **Files in this KB:** `NN-topic.md` (numbered, lowercase, dashes)

## Do
- [ FILL IN — e.g. "Test in a duplicate flow before touching the live one" ]

## Don't
- Don't put secrets/keys/tokens in these docs (only where they live).
- [ FILL IN ]

## Secrets handling
- All credentials live in [ FILL IN — n8n Credentials / a .env / a vault ], never in docs or chat.
