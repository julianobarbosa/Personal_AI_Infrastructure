# Plan: Remove Claude references from PR #788 commit

## Context

PR #788 (`feat/pai-worktree-tmux-flags`) is now Verified on GitHub, but the commit message and existing code in `pai.ts` still reference "Claude". Barbosa wants all Claude references removed from:
1. The **commit message** (Co-Authored-By trailer, "pass through to claude" body text)
2. The **code** in `pai.ts` (comments, variable names, user-facing strings)

The PR is on branch `feat/pai-worktree-tmux-flags` in fork `julianobarbosa/Personal_AI_Infrastructure`.

## File to modify

`Releases/v3.0/.claude/skills/PAI/Tools/pai.ts`

## Changes

### A. Code changes in `pai.ts`

**Variable renames:**
| Line | Current | New |
|------|---------|-----|
| 31 | `const CLAUDE_DIR` | `const PAI_DIR` |
| All | `CLAUDE_DIR` (all usages) | `PAI_DIR` |
| 485 | `const claudeResult` | `const installResult` |

**Comment/string renames (user-facing text):**
| Line | Current | New |
|------|---------|-----|
| 5 | `Managing Claude Code with dynamic MCP` | `Managing PAI sessions with dynamic MCP` |
| 9 | `Launch Claude (default profile)` | `Launch PAI (default profile)` |
| 14 | `Update Claude Code` | `Update PAI engine` |
| 232 | `Restart Claude Code to apply` | `Restart PAI to apply` |
| 441 | `// Launch Claude` | `// Launch PAI` |
| 472 | `Updating Claude Code...` | `Updating PAI engine...` |
| 483 | `// Step 2: Update Claude Code` | `// Step 2: Update PAI engine` |
| 484 | `Installing latest Claude Code...` | `Installing latest PAI engine...` |
| 487 | `Claude Code installation failed` | `PAI engine installation failed` |
| 488 | `Claude Code updated` | `PAI engine updated` |
| 593 | `Launch Claude (no MCPs, max performance)` | `Launch PAI (no MCPs, max performance)` |
| 601 | `Update Claude Code to latest version` | `Update PAI engine to latest version` |

**CANNOT change (actual binary/path references):**
- `["claude"]`, `["claude", "--version"]`, `["claude", "-p", prompt]` — these call the actual `claude` binary
- `curl -fsSL https://claude.ai/install.sh` — actual install URL
- `~/.claude` in help text — actual filesystem path

### B. Commit message

**Current:**
```
feat: Add --worktree and --tmux flags to pai.ts CLI

Add --wt/--worktree [name] and --tmux flags that pass through to
claude, enabling isolated parallel work sessions via git worktrees
and tmux. The -w short flag remains reserved for wallpaper.

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

**New:**
```
feat: Add --worktree and --tmux flags to pai.ts CLI

Add --wt/--worktree [name] and --tmux flags that pass through to
the underlying CLI, enabling isolated parallel work sessions via
git worktrees and tmux. The -w short flag remains reserved for wallpaper.
```

### C. Amend and force-push

1. `git checkout feat/pai-worktree-tmux-flags`
2. Apply code changes to `pai.ts`
3. `git add Releases/v3.0/.claude/skills/PAI/Tools/pai.ts`
4. `GIT_COMMITTER_NAME="Juliano Barbosa" GIT_COMMITTER_EMAIL="julianomb@gmail.com" git commit --amend --author="Juliano Barbosa <julianomb@gmail.com>"` with new message
5. `git push fork feat/pai-worktree-tmux-flags --force`
6. `git checkout main`

## Verification

1. `gh api repos/danielmiessler/Personal_AI_Infrastructure/pulls/788/commits --jq '.[0].commit.verification.verified'` → `true`
2. `gh api repos/danielmiessler/Personal_AI_Infrastructure/pulls/788/commits --jq '.[0].commit.message'` → no "claude" or "Claude" references
3. `grep -i claude Releases/v3.0/.claude/skills/PAI/Tools/pai.ts` → only binary calls (`["claude"]`) and the install URL remain
4. `git branch --show-current` → `main`
