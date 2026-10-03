# ERPNext fork customization registry

One entry tracks one distinct capability or intentional behavior change, including maintenance capabilities. Parts of the same capability share an entry regardless of files or commits. These records describe fork behavior, not a release changelog.

## Entries

| ID | Capability | Category | Status |
| --- | --- | --- | --- |
| [ERP-001](ERP-001-pricing-rule-customization.md) | Pricing-rule customization | product | active |
| [ERP-002](ERP-002-after-calculation-event.md) | After-calculation extension event | product | active |
| [ERP-003](ERP-003-sales-allocation-balancing.md) | Customer sales-allocation balancing | product | active |
| [ERP-004](ERP-004-product-bundle-child-validation.md) | Product Bundle child validation | product | active |
| [ERP-005](ERP-005-company-test-fixtures.md) | Company test-fixture support | test-infrastructure | active |
| [ERP-006](ERP-006-upstream-automation-removal.md) | Upstream CI/release automation removal | automation | active |
| [ERP-007](ERP-007-agent-skills-setup.md) | Agent skills setup | automation | active |

## Initial review snapshot

- ERPNext custom: `37c357bef9b33054ad5f7532616fd6f086fe0554`.
- Incorporated official base: `2597eaad5195ea4a3c89e0c2fae29e62451b742c` (15.103.1).
- Cached official `upstream/version-15`: `4cea6a7f839286b69dd30b38b0991e6ae234dac5` (15.121.6).
- Frappe dependency inspected: `853bb604f079c79d628ba617f9d4b3b502b0a626`.

The inventory compares the incorporated base to custom. The newer official ref is used only to assess upstream equivalents and merge risks. No fetch was performed for this documentation review; upstream observations are bounded to the cached commit above. Uncommitted changes are excluded.

All 22 paths in the original net diff are covered: pricing utilities and transaction controller (ERP-001), totals controller (ERP-002), Customer (ERP-003), Product Bundle (ERP-004), Company test dependency and Warehouse Type fixture (ERP-005), 13 deleted workflows (ERP-006), and skills workflow plus ignore rule (ERP-007). Import corrections support ERP-001. Historical Copilot instructions were added and removed, so they are not an active capability.

## Maintenance

Reuse IDs when extending a capability. Keep retired/upstreamed records with their reasons and replacements. After upstream integration, review every active entry, even where Git reports no conflicts; update its reviewed commits and verification results.

Source links are relative to this fork. Deleted paths are recorded as text and can be inspected at the official base with `git show <base>:<path>`. Cross-fork dependency links use immutable GitHub commits so these docs also work outside this workspace.

Verification commands in entries require an initialized Bench, a disposable test site, and the relevant apps installed. They are suggested checks, not evidence of test coverage or passing results. Bench was unavailable in this workspace during the initial review, and no runtime tests or Actions workflows were executed.
