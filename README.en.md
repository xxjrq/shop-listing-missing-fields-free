# Product Listing Missing-Field Check

Turn the visible information on a user-selected product-editing page into a practical table for specifications, media, shipping, and after-sales gaps or conflicts. Input: that page, optional merchant rules, and a user-selected browser environment. Each row includes a status (`已见`, `缺项`, `冲突`, or `待确认`), visible value, source, and suggested human action. Fields absent from the page snapshot are `待确认`; only explicitly empty values are `缺项`. It never saves, submits, or publishes.

![Product listing check promo](assets/promo.png)

## Run

```bash
npx skills add xxjrq/shop-listing-missing-fields-free
npm run self_test
node scripts/check-listing.mjs --offline-page fixtures/success-page.json --rules fixtures/rules.json
```

The fixtures are offline page-material simulations, not live collection results. A result row has category, field, status, visible value, source, and an action for a human reviewer.

## Browser prerequisite and safety

For a live page, install [Easy WebBridge on GitHub](https://github.com/xxjrq/easy-webbridge), or use the [Gitee mirror](https://gitee.com/xxjrq/easy-webbridge). The runtime uses exactly one user-specified `browserId`, one owned task session, and one task tab group. It does not broadcast to accounts, take over existing pages, or close user tabs.

Run the checker with the user-selected `browserId` and `--page-url`. It creates an owned session and tab group, confirms ownership with `list_tabs`, reads visible page text plus explicit visible control-label relationships (`label[for]`, wrapping labels, or `aria-labelledby`), then closes only its own session. It never pairs controls by order or nearby text. Source URLs retain only origin and path, removing queries and fragments. If a merchant rule targets an unread field, its expected value and action remain in the human task. Login, CAPTCHA, risk control, truncated snapshots, and malformed JSON stop the run without producing a gap table. Fields absent from the snapshot are marked `待确认`; only explicitly empty values are `缺项`. Default mode is read-only; even when draft filling is separately authorized, it must not click a final publish or send action.

Business use was informed by [ERPNext](https://github.com/frappe/erpnext), without copying source or implementation text. Licensed under [MIT](LICENSE).
