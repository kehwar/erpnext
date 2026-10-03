# ERP-006: Upstream CI/release automation removal

- Category: automation
- Status: active
- Reviewed against: [initial review snapshot](README.md#initial-review-snapshot)

## Purpose

Remove upstream-owned repository automation from the fork. The originating commit calls these workflows deprecated; detailed reasons for removing each job are not recorded. Treat removal of test checks as an explicit operational tradeoff, not evidence that tests are unnecessary.

## Behavior and implementation

Originating commit: `c77c3756a9`. Deletes these paths under `.github/workflows/`:

- `docker-release.yml`
- `docs-checker.yml`
- `label-base-on-title.yml`
- `labeller.yml`
- `linters.yml`
- `patch.yml`
- `patch_faux.yml`
- `release.yml`
- `release_notes.yml`
- `semantic-commits.yml`
- `server-tests-mariadb-faux.yml`
- `server-tests-mariadb.yml`
- `server-tests-postgres.yml`

These files exist at the incorporated official base; inspect their historical contents with `git show 2597eaad5195ea4a3c89e0c2fae29e62451b742c:.github/workflows/<filename>`.

The fork no longer defines these lint, database/server-test, patch/migration-test, documentation, labeling, semantic-commit, release-note, release, and Docker jobs. The only current workflow is [agent skills setup](ERP-007-agent-skills-setup.md), which does not replace tests or releases. External CI and GitHub branch protection are not audited by this record.

## Verification

Check workflow inventory from this repository:

```bash
find .github/workflows -maxdepth 1 -type f -print
git show --stat c77c3756a9
```

Initial review: all 13 deletions confirmed against the base. No GitHub Actions execution or repository-settings review performed. Before relying on this policy, check required status checks and decide how custom changes and upstream integrations will be tested.

## Upstream maintenance

A future merge can reintroduce upstream workflows or add new ones. Review automation changes explicitly, preserving intended fork-specific behavior rather than blindly deleting every new job.

Retire or narrow this entry when suitable fork-owned automation is adopted or individual upstream jobs become appropriate. Document replacement checks before treating lost coverage as restored.
