---
Your Timeline

NOW ──────────────► HANDOFF ──────────────► AFTER
     Push to both      │       Personal = archive
     repos             │       Company = their copy
                       │
                    Separate them

---
Setup (Now)

# First, create the repo at WindyCityWire (empty, no README)
# Then run these commands:

# Add company as second push destination
git remote set-url --add --push origin https://github.com/WindyCityWire/savvy-next.git

# Verify setup - should show TWO push URLs
git remote -v
# origin  https://github.com/Aztec03hub/savvy-next (fetch)
# origin  https://github.com/Aztec03hub/savvy-next (push)
# origin  https://github.com/WindyCityWire/savvy-next (push)

# Push everything to both
git push origin --all
git push origin --tags

Now every git push goes to both repos automatically.

---
At Handoff (When You Leave)

Remove the company URL so your pushes only go to your personal repo:

# Remove company from push URLs
git remote set-url --delete --push origin https://github.com/WindyCityWire/savvy-next.git
git remote set-url --delete --push origin https://github.com/WindyCityWire/vscode-mcp-server.git
git remote set-url --delete --push origin https://github.com/WindyCityWire/NavTrack.git
git remote set-url --delete --push origin https://github.com/WindyCityWire/caddy2-linux.git
git remote set-url --delete --push origin https://github.com/WindyCityWire/TrackerAPI.git
git remote set-url --delete --push origin https://github.com/WindyCityWire/caddy2-windows.git

OR

# Remove personal from push URLs
git remote set-url --delete --push origin https://github.com/Aztec03hub/savvy-next.git
git remote set-url --delete --push origin https://github.com/Aztec03hub/vscode-mcp-server.git
git remote set-url --delete --push origin https://github.com/Aztec03hub/NavTrack.git
git remote set-url --delete --push origin https://github.com/Aztec03hub/caddy2-linux.git
git remote set-url --delete --push origin https://github.com/Aztec03hub/TrackerAPI.git
git remote set-url --delete --push origin https://github.com/Aztec03hub/caddy2-windows.git

# Verify - back to single push URL
git remote -v
# origin  https://github.com/Aztec03hub/savvy-next (fetch)
# origin  https://github.com/Aztec03hub/savvy-next (push)

From that point:
- Your personal repo: Your archive, you can still push/modify
- Company repo: Their copy, they maintain it going forward
- No connection: They're completely independent

---
Bonus: Clean Separation Checklist

Before handoff, you might want to:

1. Remove any personal secrets from company repo
2. Update README with handoff notes for the team
3. Create a release tag marking the handoff point:
git tag -a v1.0-handoff -m "Handoff to WindyCityWire team"
git push origin --tags  # Pushes to both while still connected

---

/////////////////////////////////////////////////////////////////////////

To add all file changes, commit, and push:
git add -A
git commit -m "chore: housekeeping"

---

Our git has commitlint set up, which enforces "Conventional Commits" format.
The Format:

type(scope): subject

type: subject      ← scope is optional

Examples:
feat: add user authentication
fix: resolve login timeout issue
docs: update README with setup instructions
chore: reorganize project files
refactor: simplify database queries

---

My Allowed Types

From my commitlint.config.cjs:

| Type     | Use for                  |
|----------|--------------------------|
| feat     | New features             |
| fix      | Bug fixes                |
| docs     | Documentation            |
| style    | Formatting, whitespace   |
| refactor | Code restructuring       |
| test     | Tests                    |
| chore    | Maintenance tasks        |
| build    | Build system changes     |
| ci       | CI/CD changes            |
| perf     | Performance improvements |
| revert   | Reverting commits        |
| merge    | Merge commits            |
| step     | Workflow step (custom)   |
| task     | Task completion (custom) |

---

git remote -v

git remote remove origin

git remote add origin https://github.com/WindyCityWire/caddy2-windows.git

git remote set-url --add --push origin https://github.com/WindyCityWire/caddy2-windows.git
git remote set-url --add --push origin https://github.com/Aztec03hub/caddy2-windows.git

git remote -v

git push -u origin main

git push origin --all
git push origin --tags