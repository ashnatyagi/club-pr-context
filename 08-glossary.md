# 08 — Glossary

> Internal names, acronyms, and terms — so no agent has to guess what a word means.

| Term | Meaning |
|------|---------|
| Club PR | [ FILL IN — the brand / client / project this is all for; automation generates PR/social content for clubs and their events ] |
| Flow | An n8n workflow, named "Club PR - Flow [Letter] - [Purpose]" |
| Reel | Short vertical video for social media (Instagram-style) |
| Asset | A row in the master Google Sheet representing one piece of content (an image or video) tied to a club/event |
| Zoompan | Cloudinary's Ken Burns-style pan/zoom video effect, used to turn a still image into a short video |
| Fast track | Sheet column (`fast_track`) — `'Y'` marks a tagged asset for immediate automated processing by Flow B |
| QC (Quality Control) | The Groq Vision check that judges whether a generated reel is fit to post (legible text, no cropping issues, good contrast) |
| Posting Queue | Where reels that pass QC are placed, ready for a (separate, unconfirmed) publishing step |
| Vibe tag / overlay zone | Sheet columns describing an asset's mood/style and where text should be overlaid — set upstream of Flow B |
| Reactive generation | Reels generated in reaction to assets appearing/being tagged in the sheet, rather than fully pre-scheduled content |
