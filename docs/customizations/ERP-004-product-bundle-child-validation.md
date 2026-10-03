# ERP-004: Product Bundle child-validation correction

- Category: product
- Status: active
- Reviewed against: [initial review snapshot](README.md#initial-review-snapshot)

## Purpose

Detect active nested Product Bundles using the bundle's item relationship rather than assuming its document name matches the item code. This adjusts an internal validation lookup. The motivating dataset and why an app-level override was unsuitable are not documented.

## Behavior and implementation

[Product Bundle controller](../../erpnext/selling/doctype/product_bundle/product_bundle.py), `ProductBundle.validate_child_items()`, changes the existence filter:

```python
# Official base
{"name": item.item_code, "disabled": 0}
# Custom
{"new_item_code": item.item_code, "disabled": 0}
```

The existing error still rejects a child that is an active bundle. Disabled bundles remain excluded. `autoname()` normally assigns `name = new_item_code`, so both lookups are equivalent for normally named records. The distinguishing case requires a record where the document name and item code differ; its practical occurrence has not been established.

Originating commit: `de29fc6fad`.

## Verification

No fork-specific regression assertion was added. The existing [Product Bundle helper module](../../erpnext/selling/doctype/product_bundle/test_product_bundle.py) contains fixture data and `make_product_bundle()`, but no discoverable test cases. Running that module through Bench does not verify this change.

Add a focused regression test using a lookup mock or a valid fixture with a differing bundle name/item code, plus active/disabled child bundles and ordinary items. Once actual tests exist in that module, run:

```bash
bench --site <test-site> run-tests --app erpnext --module erpnext.selling.doctype.product_bundle.test_product_bundle
```

Establish a realistic reproducer before claiming a production bug is fixed.

Initial review: source/history inspected; runtime tests not run.

## Upstream maintenance

The cached newer official version still queries by `name`. Review changes to naming, renaming, and nested-bundle rules before preserving the filter mechanically.

Retire if upstream uses the item relationship or guarantees equivalence for all supported records and the original reproducer no longer needs the change.
