# ERP-007: Agent skills setup

- Category: automation
- Status: active
- Reviewed against: [initial review snapshot](README.md#initial-review-snapshot)

## Purpose

Populate agent-session skills from the user's shared skills repository and Frappe fork without committing copied material into ERPNext. This is fork-specific development tooling, separate from product behavior and CI/release policy.

## Behavior and implementation

- [Copilot setup workflow](../../.github/workflows/copilot-setup-steps.yml): checks out ERPNext, shallow-clones `kehwar/skills`, and copies `skills/*` into `.github/skills/`. It then sparse-clones `kehwar/frappe`, copies `.agents/skills/*` into the same destination, and removes temporary clones.
- [.gitignore](../../.gitignore): excludes `.github/skills/` so copied session material stays untracked.
- The job uses `ubuntu-latest`, `actions/checkout@v4`, and read-only contents permission. It supports manual dispatch and push/pull-request triggers for edits to the workflow itself.

Both clones use remote default branches without pinned commits; skill contents can change between runs. Files from both sources share a destination, so name collisions and copy order matter. Empty/missing source directories and network failures can fail the job. The workflow name alone does not prove a live agent session has consumed the copied skills.

Originating/supporting commits:
- `f9aadee1bc`: setup workflow.
- `a0bfa1a8e9`: clone and permission adjustments.
- `12caa7959d`: directory-navigation fix.
- `21146224f4`: copied-skills ignore rule.

## Verification

Inspect the workflow and confirm the ignore rule from this repository:

```bash
git check-ignore .github/skills/example/SKILL.md
```

In a disposable Actions run/session, check both source trees exist, copied skills are discoverable, collisions are handled acceptably, and the worktree remains free of copied untracked files. Initial review: workflow and ignore rule inspected; no network setup job or agent-session integration test executed.

## Upstream maintenance

Review workflow/API compatibility, source directory changes, default-branch selection, and skill provenance whenever automation is updated. Fetching instructions from unpinned repositories is a trust/reproducibility dependency.

Retire if the agent platform supplies equivalent managed skill discovery, or this fork no longer uses the setup job; remove the ignore rule only if copied session material is no longer generated there.
