# 02 — Architecture (how it all fits together)

> The big picture, in plain words. Update this whenever the shape of the system changes.

## One-paragraph summary
[ FILL IN — e.g. "Triggers (news / events / manual) feed n8n workflows hosted on a Bluehost VPS. Workflows call AI models to generate scripts + media, assemble a reel, and publish/schedule it to social platforms." ]

## Data flow (end to end)
<!-- Keep this as a simple ordered list. This replaces a diagram. -->
1. **Trigger:** [ FILL IN — what starts a run? ]
2. **Input gathering:** [ FILL IN ]
3. **AI generation:** [ FILL IN — which models, what they produce ]
4. **Assembly:** [ FILL IN — how the reel is built ]
5. **Review / approval:** [ FILL IN — manual or automatic? ]
6. **Publish / schedule:** [ FILL IN — where does it go ]
7. **Logging:** [ FILL IN — where results are recorded ]

## Environments
| Environment | Where | Purpose |
|-------------|-------|---------|
| Production | Bluehost VPS ([ domain / IP ]) | Live workflows |
| Test / staging | [ FILL IN or "none yet" ] | Safe place to try changes |

## Key external dependencies
<!-- Things that, if they break, break us. -->
- [ FILL IN — e.g. Instagram API, an AI provider, a storage bucket ]
