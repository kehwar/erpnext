# ERP-001: Pricing-rule customization

- Category: product
- Status: active
- Reviewed against: [initial review snapshot](README.md#initial-review-snapshot)

## Purpose

Allow custom apps/scripts to extend pricing behavior and explicitly reevaluate pricing rules without monkey patching core functions. The official base lacks the two server postprocessing hooks and the reusable client recalculation helper. The related free-item response guard protects this pricing flow.

These hooks, helper, guard, and import fixes implement one pricing capability, not separate registry entries.

## Behavior and implementation

- [Pricing utilities](../../erpnext/accounts/doctype/pricing_rule/utils.py): `apply_pricing_rule_on_transaction(doc)` and `get_product_discount_rule(pricing_rule, item_details, args=None, doc=None)` use `tweaks.wrap_with_hook` with hook names matching their function names.
- The original function executes first. The last registered app hook then receives the original arguments plus `_hook`, whose `result` contains the original return value. The hook's return value replaces that return value. These are postprocessing hooks, not replacements for original execution; earlier registered hooks are not called.
- Both decorated functions normally return `None`, and core callers discard their return values. Effective hooks must mutate the supplied `doc` or `item_details` in place; returning a replacement document or discount payload alone does not change core pricing. See [Accounts controller](../../erpnext/controllers/accounts_controller.py) and [Pricing Rule controller](../../erpnext/accounts/doctype/pricing_rule/pricing_rule.py) for call sites.
- [Transaction controller](../../erpnext/public/js/controllers/transaction.js): `reapply_pricing_rules()` saves `ignore_pricing_rule`, sets it to 1, triggers its handler, restores the saved value, then invokes `apply_pricing_rule()` in a serial promise chain.
- In `apply_price_list()`, discount application skips returned children without both `doctype` and `name`. The originating commit describes free items, but the code checks identifiers, not `is_free_item`; identified free items are not categorically excluded.

Dependency: the Frappe fork's [`frappe.tweaks.wrap_with_hook`](https://github.com/kehwar/frappe/blob/853bb604f079c79d628ba617f9d4b3b502b0a626/frappe/tweaks.py). This import is absent from official Frappe at the incorporated base. Consuming app hook registrations and intended business policies are outside this repository.

Originating commits:
- `368c871105`: discount response guard.
- `33da57315f`: recalculation helper.
- `37471d5873`, `3d69105326`: server hooks.
- `9d7b186a12`, `42b2b83789`: hook import corrections.

## Verification

No fork-specific regression assertions were added for these changes. Existing [pricing-rule tests](../../erpnext/accounts/doctype/pricing_rule/test_pricing_rule.py) exercise standard server pricing behavior but do not establish coverage of the new hook contracts or client helper.

From an initialized Bench with a disposable test site:

```bash
bench --site <test-site> run-tests --app erpnext --module erpnext.accounts.doctype.pricing_rule.test_pricing_rule
```

Add targeted checks for no registered hook, last-hook precedence, original execution before the hook, argument forwarding and replacement return values. In browser tests, verify rule clearing/reapplication, preservation of both values of `ignore_pricing_rule`, and discount responses with and without row identifiers. Include failure paths: the helper has no `finally` restoration if the ignore-rule handler rejects.

Initial review: source/history inspected; runtime and browser checks not run.

## Upstream maintenance

The cached newer official ref still lacks these hooks/helper and still applies the relevant item discount unconditionally. Review pricing response shapes, rule reset behavior, function signatures, and the Frappe hook provider after every merge.

Retire components only when official extension points and behavior cover the consuming apps' requirements; keep this entry active while any part remains necessary. The general after-totals event is separately tracked by [ERP-002](ERP-002-after-calculation-event.md).
