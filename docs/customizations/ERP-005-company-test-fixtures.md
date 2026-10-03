# ERP-005: Company test-fixture support

- Category: test-infrastructure
- Status: active
- Reviewed against: [initial review snapshot](README.md#initial-review-snapshot)

## Purpose

Ensure Company test setup can resolve the `Transit` Warehouse Type used by default warehouse creation. This is a test-environment repair, not a production warehouse capability.

## Behavior and implementation

- [Company tests](../../erpnext/setup/doctype/company/test_company.py): add `Warehouse Type` alongside `Fiscal Year` in `test_dependencies`.
- [Warehouse Type fixture](../../erpnext/stock/doctype/warehouse_type/test_records.json): supply a record named `Transit`.
- Dependency context: [Company controller](../../erpnext/setup/doctype/company/company.py), `create_default_warehouses()`, creates a Goods In Transit warehouse linked to this type.

The official base lacks the explicit test dependency and fixture. The custom test setup makes the linked type available through the test-record dependency loader. No production setup code changes.

Originating commit: `74edab2416`.

## Verification

This change adds setup data, not a regression assertion. On a disposable test site without relying on preexisting Transit records, run:

```bash
bench --site <test-site> run-tests --app erpnext --module erpnext.setup.doctype.company.test_company
```

Verify the dependency loader creates Transit before Company fixture insertion and that default warehouse creation succeeds. Check repeated setup for duplicate-record handling.

Initial review: dependency declaration, fixture, and Company source inspected; runtime tests not run.

## Upstream maintenance

The cached newer official ref still lacks both additions. Check test-loader behavior, any upstream Warehouse Type fixtures, and Company warehouse defaults after merges.

Retire when upstream test setup reliably supplies the required type, or default warehouse creation no longer requires it.
