# 06 — Decision Log

> Why we chose what we chose. Stops agents (and future-you) from re-litigating settled choices.
> Newest at top. Never delete a decision — if it's reversed, add a new entry that supersedes it.

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
