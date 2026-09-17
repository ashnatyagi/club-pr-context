# 06 — Decision Log

> Why we chose what we chose. Stops agents (and future-you) from re-litigating settled choices.
> Newest at top. Never delete a decision — if it's reversed, add a new entry that supersedes it.

---

### [ 2026-09-17 ] Ship a defensive fix over chasing an unconfirmed root cause (video-tagging bug)
- **Decision:** For the `Is Video For AI Tag?` bug (two real videos both hit the false/needs_review branch despite `source_type: "video"` in the input), ship a defensive condition — `String($json.source_type || '').trim().toLowerCase() === 'video'` — instead of continuing to hunt for the exact original cause.
- **Why:** n8n's test-execution tooling can't cleanly replay a single node partway through a Telegram-Trigger workflow without either a live message or fully mocking the binary file chain (Get a file → Cloudinary upload); repeated attempts (pinning data, mock Telegram payloads, expression previews) all either required a live webhook or risked polluting production with fake pinned data. A defensive condition costs nothing if the original value was already clean, and fixes the bug outright if it was whitespace/case/type-related.
- **Alternatives considered:** Keep digging with more elaborate mocks (rejected for now: time cost vs. a fix that's very likely correct or at worst harmless); wait indefinitely for a "clean" root cause before touching the node (rejected: leaves video tagging broken with no ETA). If this fix doesn't hold on the next live video, the next step is capturing the exact raw execution JSON for that node rather than trusting the editor's rendering of the condition's value field.

---

### [ 2026-09-16 ] Run the project via an autonomy layer (agents act, Ashna maintains)
- **Decision:** Agents work mostly on their own using this KB, keeping the high-level goal in mind, and only pull Ashna in for "red light" maintenance moments (live publishing, credentials, deletions, schema/brand calls). Codified in `10-operating-principles.md`.
- **Why:** Ashna wants to stop being in the loop for routine work and only step in when truly needed.
- **Alternatives considered:** Approving every step (rejected: that's the problem we're solving); full autonomy with no red lines (rejected: risky for live posting, money, and brand).

---

### [ 2026-09-16 ] One Google Sheet as the whole database
- **Decision:** All flows share one sheet ("Club PR - Asset Bank & Queue"), Sheet1 for intake, "Posting Queue" for output.
- **Why:** Simple, visible, easy to edit by hand; no separate DB to run.
- **Trade-off / alternative:** A real database would be more robust, but the sheet is treated as a shared contract (see `05-conventions.md`) to manage the risk.

---

### [ 2026-09-16 ] Use a shared Markdown knowledge base for project context
- **Decision:** Keep all project context as plain `.md` files in one shared folder that every agent reads.
- **Why:** Switching agents/threads was losing context and wasting tokens re-explaining the system.
- **Alternatives considered:** Re-explaining each time (rejected: slow, costly); one AI-specific doc (rejected: not accessible to other agents).

---

<!-- TEMPLATE
### [ YYYY-MM-DD ] [ short title ]
- **Decision:**
- **Why:**
- **Alternatives considered:**
-->
