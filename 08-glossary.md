# 08 — Glossary

> Internal names, acronyms, and terms — so no agent has to guess what a word means.

| Term | Meaning |
|------|---------|
| Club PR | The business: keyword-rich Instagram pages that post daily club content to generate leads (clubs / PR), given free first then charged. This automation is the content engine behind it. See `01-vision-and-goals.md`. |
| Lead | A user who contacted a page and can be handed to a club/PR — free first, then paid |
| Evergreen content | Posts that stay relevant over time (not tied to one event/date) — part of the daily mix |
| Flow | An n8n workflow, named "Club PR - Flow [Letter] - [Purpose]" |
| Reel | Short vertical video for social media (Instagram-style) |
| Asset | A row in the master Google Sheet representing one piece of content (an image or video) tied to a club/event |
| Zoompan | Cloudinary's Ken Burns-style pan/zoom video effect, used to turn a still image into a short video |
| Fast track | Sheet column (`fast_track`) — `'Y'` marks a tagged asset for immediate automated processing by Flow B |
| QC (Quality Control) | The Groq Vision check that judges whether a generated reel is fit to post (legible text, no cropping issues, good contrast) |
| Posting Queue | Where reels that pass QC are placed, ready for a (separate, unconfirmed) publishing step |
| Vibe tag / overlay zone | Sheet columns describing an asset's mood/style and where text should be overlaid — set upstream of Flow B |
| Reactive generation | Reels generated in reaction to assets appearing/being tagged in the sheet, rather than fully pre-scheduled content |
