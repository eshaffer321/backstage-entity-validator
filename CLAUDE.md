# Fork Strategy & Development Workflow

This is a fork of [RoadieHQ/backstage-entity-validator](https://github.com/RoadieHQ/backstage-entity-validator) with improvements and bug fixes.

## Why Fork?

**Upstream Status (as of Oct 2025):**
- ✅ Still maintained (last commit June 2025)
- ⚠️ Slow to merge PRs (dependabot PRs sitting 6+ months)
- ⚠️ Community concerns about maintenance velocity
- 📊 78 stars, 15 forks - modest community

**Decision:** Dual-track development - improve rapidly on our fork while contributing back upstream when possible.

## Repository Structure

```
upstream (RoadieHQ/backstage-entity-validator)
    ↓
main branch (tracking upstream/main)
    ↓
    ├── production branch      ← Your working branch (all improvements)
    │                            Ship fixes here, use in production
    │
    └── upstream-pr branch     ← Clean branch for upstream PRs
                                 Cherry-pick individual fixes for submission
```

## Workflow

### Making Improvements

```bash
# Work on production branch
git checkout production

# Make your changes, commit, push to your fork
git commit -am "Fix: Report all validation errors instead of failing on first"
git push origin production

# Use this branch in production/CI/workflows
```

### Submitting to Upstream

```bash
# Switch to upstream-pr branch
git checkout upstream-pr

# Cherry-pick the specific improvement
git cherry-pick <commit-hash-from-production>

# Create focused PR to upstream
gh pr create --repo RoadieHQ/backstage-entity-validator \
  --title "Fix: Report all validation errors" \
  --body "Improves CI efficiency by showing all errors in one run..."

# Switch back to production
git checkout production
```

## Branches

- **`main`** - Tracks upstream/main, kept in sync
- **`production`** - Your default working branch, all improvements live here
- **`upstream-pr`** - Staging branch for individual upstream PRs (keep clean)

## Git Remotes

- **`origin`** - Your fork (eshaffer321/backstage-entity-validator)
- **`upstream`** - Original repo (RoadieHQ/backstage-entity-validator)

## PR Strategy for Upstream

**Prioritize small, focused PRs:**

1. **Bug fixes** (highest merge chance)
   - Example: fail-fast → report all errors
   - Clear before/after behavior

2. **Performance improvements** (good chance)
   - Measurable impact
   - No breaking changes

3. **New features** (lower chance)
   - Strong justification needed
   - Solves common use case

4. **Refactors** (lowest chance)
   - Only if critical or enables other work

**Each PR should include:**
- Clear problem statement
- Before/after behavior
- Why it matters (CI efficiency, DX, etc.)
- Test results
- Backward compatibility notes

## Syncing with Upstream

```bash
# Update main from upstream
git checkout main
git fetch upstream
git merge upstream/main
git push origin main

# Rebase production if needed
git checkout production
git rebase main

# Rebase upstream-pr if needed
git checkout upstream-pr
git rebase main
```

## Files That Stay on Fork

This file (`CLAUDE.md`) should **NOT** be submitted to upstream:
- It's specific to your fork strategy
- Documents your workflow, not the tool itself
- Added to `.gitignore` on `upstream-pr` branch

## Current Improvements

### Completed
- [x] **Fix fail-fast behavior** - Now validates ALL files and reports ALL errors
  - Previous: Stopped at first validation error
  - Now: Validates all files, collects all errors, shows summary
  - Added summary statistics (Total/Passed/Failed counts)
  - Exit code 1 if any failures, 0 if all pass
  - Verbose mode shows detailed error list
  - Quiet mode shows errors as they occur + summary
  - Fully tested with TDD (red-green-refactor)

### Planned/Future
- [ ] Add --fail-fast flag to restore old behavior (optional)
- [ ] Improve error output formatting (colors, better alignment)
- [ ] Add JSON output mode for CI integration

### Submitted to Upstream
- None yet (improvement ready for submission)

### Merged Upstream
- None yet

## Philosophy

**Don't let upstream block you.**

- ✅ Ship improvements on `production` immediately
- ✅ Use your fork in production workflows
- ✅ Submit thoughtful PRs to upstream
- ✅ Expect ~50% merge rate at best
- ✅ Be a good OSS citizen, but stay productive

If a PR sits for 2+ months, it's not personal - just move on. Your fork works, you're helping users, and upstream can merge whenever they're ready.

## Quick Reference

```bash
# Daily work
git checkout production
# make changes
git push origin production

# Preparing upstream PR
git checkout upstream-pr
git cherry-pick abc123
git push origin upstream-pr
gh pr create --repo RoadieHQ/backstage-entity-validator ...

# Sync from upstream
git checkout main
git pull upstream main
git checkout production
git rebase main
```

## Questions?

Check the upstream repo for their contributing guidelines, though this fork prioritizes shipping over waiting for approval.
