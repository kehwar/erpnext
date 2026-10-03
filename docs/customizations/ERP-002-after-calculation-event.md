# ERP-002: After-calculation extension event

- Category: product
- Status: active
- Reviewed against: [initial review snapshot](README.md#initial-review-snapshot)

## Purpose

Give custom form scripts an explicit completion event after taxes and totals calculation, avoiding replacement or monkey patching of the controller. The official base has no corresponding completion trigger.

This is a general transaction extension point, independently useful outside pricing-rule customization.

## Behavior and implementation

[Taxes and totals controller](../../erpnext/public/js/controllers/taxes_and_totals.js), `calculate_taxes_and_totals(update_paid_amount)`, now awaits:

```js
await this.frm.trigger("after_calculate_taxes_and_totals");
```

It runs after the method's existing calculation flow and `refresh_fields()`. Registered async form handlers delay completion of this method, although callers must themselves await it to observe completion. Handler errors can reject the calculation promise; a handler that invokes the calculation again can recurse.

Originating commit: `189e7e0667`.

## Verification

No fork-specific test assertions were added. Planned browser checks:
- A registered handler sees updated totals and runs after field refresh.
- An async handler completes before the calculation promise resolves.
- An absent handler preserves standard behavior.
- Failure and recursion behavior are understood by consuming scripts.

Use a disposable transaction form on a test site; automated browser coverage is still needed. Initial review: source/history inspected; browser checks not run.

## Upstream maintenance

The cached newer official controller lacks this event. Check the trigger's placement against new early returns, additional calculation stages, and async changes after upstream integration.

Retire if an official completion event supplies equivalent timing and async semantics and consumers have migrated. No consuming scripts are included in this fork's change.
