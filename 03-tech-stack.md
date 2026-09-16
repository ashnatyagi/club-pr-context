# 03 — Tech Stack (tools & how we use them)

> Every tool in the project. If an agent needs to know "what do we use for X and how", it's here.
> ⚠️ Never write real passwords, API keys, or tokens in this file. Only say WHERE a credential lives (e.g. "in n8n Credentials as 'IG-main'").

| Tool | What it's for | How we use it | Where it lives | Credential location |
|------|---------------|---------------|----------------|--------------------|
| n8n | Workflow automation engine | Hosts all the flows | Self-hosted on Bluehost VPS | n8n internal Credentials |
| Bluehost VPS | Server / hosting | Runs n8n + serves files | Bluehost account | — |
| [ AI model, e.g. GPT-4 / Claude ] | [ script + caption generation ] | [ via n8n AI node / API ] | [ provider ] | [ n8n cred name ] |
| [ Media / video tool ] | [ reel assembly ] | [ FILL IN ] | [ FILL IN ] | [ FILL IN ] |
| [ Social platform, e.g. Instagram ] | [ publishing reels ] | [ FILL IN ] | [ FILL IN ] | [ FILL IN ] |
| [ Database / storage ] | [ FILL IN ] | [ FILL IN ] | [ FILL IN ] | [ FILL IN ] |

## Notes / gotchas
<!-- Things that trip people up. e.g. "VPS restarts n8n nightly at 3am", "IG token expires every 60 days". -->
- [ FILL IN ]
