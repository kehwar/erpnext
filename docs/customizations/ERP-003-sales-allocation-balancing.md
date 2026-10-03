# ERP-003: Customer sales-allocation balancing

- Category: product
- Status: active
- Reviewed against: [initial review snapshot](README.md#initial-review-snapshot)

## Purpose

Save customers with automatic balancing of sales-team percentages rather than requiring users to correct their total manually. Core Customer validation owns the rejection; the exact reason a supported app-level override was unsuitable is not recorded in the originating commit.

## Behavior and implementation

[Customer controller](../../erpnext/selling/doctype/customer/customer.py), `Customer.validate()`:

- Official base: a nonempty sales team whose percentages do not sum exactly to 100 raises a validation error.
- Custom: deviations within `0.00001` are accepted. Otherwise, replace the final member's percentage with `round(100 - sum(previous members), 2)`.

This changes the saved allocation, not merely floating-point tolerance. A single member is adjusted to 100; an empty team is unchanged. If preceding members exceed 100, the computed last allocation can be negative. Two-decimal rounding does not guarantee an exact total when preceding values have finer precision. This block adds no post-adjustment validation.

Originating commit: `b292abe75b`.

## Verification

No regression assertions were added for automatic balancing. Existing [Customer tests](../../erpnext/selling/doctype/customer/test_customer.py) provide broader regression checks:

```bash
bench --site <test-site> run-tests --app erpnext --module erpnext.selling.doctype.customer.test_customer
```

Add cases for an exact total, near-tolerance totals, under/over-allocation, one/zero rows, finer precision, and preceding percentages above 100. Verify persisted values and intended handling of negative results.

Initial review: source/history inspected; runtime tests not run.

## Upstream maintenance

The cached newer official version uses precision-aware `flt` when validating totals but still rejects invalid allocation instead of rebalancing. Its rounding improvement is not equivalent to this behavior change.

During merges, preserve or explicitly revise the auto-balancing policy and review interactions with field precision and other sales-team validators. Retire if official behavior provides the desired policy or the fork intentionally returns to strict validation.
