# Claude Code Skills & Agents — BurgerBlast

Custom commands and sub-agents available in this project when using [Claude Code](https://claude.ai/code).

---

## Commands  `.claude/commands/`

### `/updategit`
Publishes the project to GitHub end-to-end in one shot.

**What it does (in order):**
1. **Security scan** — greps for hardcoded passwords, API keys, tokens, private keys, AWS credentials, and DB connection strings. Blocks the push if anything is found.
2. **README check** — creates or leaves `README.md` as needed.
3. **GitHub Actions** — scaffolds `.github/workflows/deploy.yml` for GitHub Pages if missing.
4. **Commit & push** — stages changed files (skipping secrets), writes a commit message, and pushes to `origin main`. Creates the GitHub repo via `gh` if it doesn't exist yet.
5. **GitHub Pages** — enables Pages with the workflow build type if not already active.
6. **Repo About** — sets the repo description, homepage URL, and topics.
7. **Summary report** — prints a status table and the live Pages URL.

**Usage:** type `/updategit` in Claude Code.

---

## Sub-agents  `.claude/agents/`

### `whatsapp-widget`
Adds or updates the floating WhatsApp chat widget in `index.html`.

**Trigger phrases (Claude auto-delegates):**
- "add the WhatsApp widget"
- "update the WhatsApp number"
- "change the quick-reply messages"
- "remove the WhatsApp widget"

**What it implements:**
- Fixed green FAB button (bottom-right, WhatsApp icon, hover scale animation)
- Click-to-open popup with a green header and 5 suggestive quick-reply buttons
- Each button opens `wa.me/<number>?text=<message>` in a new tab
- Click-outside or ✕ closes the popup
- Responsive (smaller layout on ≤ 480 px)

**Tools:** `Read`, `Edit` only — never rewrites the whole file.

**Usage:** just describe what you want, e.g.:
> "Add the WhatsApp widget, my number is 15551234567"
> "Change the 'nearest location' query to 'Book a table'"

---

## Adding more skills

| Type | Location | Docs |
|------|----------|------|
| Slash command | `.claude/commands/<name>.md` | Any markdown; Claude follows the instructions when `/name` is invoked |
| Sub-agent | `.claude/agents/<name>.md` | YAML frontmatter (`name`, `description`, `tools`, `model`, …) + system prompt |
| Global command | `~/.claude/commands/<name>.md` | Available across all projects |
| Global agent | `~/.claude/agents/<name>.md` | Available across all projects |
