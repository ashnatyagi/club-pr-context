# 06 — Decision Log

> Why we chose what we chose. Stops agents (and future-you) from re-litigating settled choices.
> Newest at top. Never delete a decision — if it's reversed, add a new entry that supersedes it.

---

### [ 2026-09-17 ] Verify a fix by actually running it, not by reading the config
- **Decision:** A fix is not "done" until it has been *executed* against real data. Reading a node's parameters and concluding they look right does not count. Where a live trigger is not available, copy the node into a throwaway workflow, feed it real data, run it, and archive the scratch workflow afterwards.
- **Why:** On 2026-09-17 the video path was declared fixed twice before it actually was. Both times the config looked correct on screen. What the screen could not show was (a) that the engine was running a stale compiled copy, (b) that a downstream node had a syntax error, (c) that a JSON body was missing a quote, and (d) that a body was in Fixed rather than Expression mode. Each of those only surfaced by running the thing.
- **Alternatives considered:** Keep reading configs more carefully (rejected — three of the four faults were invisible to inspection); wait for Ashna to send a test video after every change (rejected as the only method — it makes her the test harness and burns a lot of her time, though it stays the final confirmation).

---

### [ 2026-09-17 ] Treat a never-executed branch as unverified, however good it looks
- **Decision:** Any workflow branch that has never run in production is assumed broken until proven otherwise. When a routing bug is fixed and a dormant branch becomes reachable, walk the whole branch node by node before declaring the feature working.
- **Why:** Flow A's video branch had four independent faults sitting in it. They were invisible precisely because the branch never executed — n8n only reports an error when a node actually runs, so a syntax error or a malformed body can sit there indefinitely. Fixing the routing did not fix video tagging; it only revealed the next fault, three times over.
- **Alternatives considered:** Fix and ship one node at a time waiting for a live video between each (rejected — far too slow and it wastes Ashna's attention); assume the rest of the branch is fine (rejected — that assumption is exactly what failed here).

---

### [ 2026-09-17 ] When editing several nodes at once, verify each one on its own path
- **Decision:** If one change is applied to N nodes (e.g. a model-name swap across every Groq node), each node must be verified on its own execution path. "The other two worked" is not evidence about the third.
- **Why:** The qwen3.6 → qwen3.8 switch touched three Groq nodes. Two were on the image path and were confirmed live; the third was on the unreachable video path. A dropped closing quote in that third node went unnoticed for hours and was reported as verified. The verification was real but did not cover what it was claimed to cover.
- **Alternatives considered:** Verify only a sample (rejected — this incident is the failure mode); avoid bulk edits (rejected — they are usually correct and efficient; the gap is in verification, not in the edit).

---

### [ 2026-09-17 ] Ship a defensive fix over chasing an unconfirmed root cause (video-tagging bug)
- **SUPERSEDED 2026-09-17 (same day):** the defensive condition was not the fix and has been removed. The real causes were a stale compiled node version plus three further faults on the never-executed branch — see `07-changelog.md`. Kept here as a record of the reasoning at the time.
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
