# Dev log

A dated record of progress, summarised from the project's real commit history (about 210 commits between 16 and 30 September 2026). Update it as the work moves; don't backfill.

## 16–20 Sep — foundation and tenancy
Repository setup with CI (Pint, Rector, PHPStan over the tests). Reworked the starter's team model into stores: UUID-keyed tenants with handles, domains, per-store roles and permissions, platform admin. Storefronts are served by hostname, with a store lifecycle (suspend, reinstate, close) and onboarding. Staff invitations and ownership transfer. Architecture tests, a Postgres test group, a coverage floor and browser end-to-end flows. Hardening: Eloquent strict mode, security headers, rate limiting, dependency audits.

## 19–23 Sep — catalogue
Products with default variants, options, collections, and per-variant, per-location inventory with an audit trail. Store-scoped media on object storage, with a test proving cross-store isolation. Product search with Scout, scoped by store.

## 24–25 Sep — storefront
Merchant pages, navigation menus, search, 404, robots and sitemap. A theme editor with draft saving, signed store-bound previews and publish/discard, guarded against stale drafts.

## 25–27 Sep — carts, orders and checkout
Store-scoped customer accounts with their own guard. Carts priced live with VAT against current stock. Cash-on-delivery order placement as one transaction, per-store order numbers, a signed order status page, and confirmation emails to shopper and merchant.

## 30 Sep — accounts and admin
Customer registration that claims a guest record, email verification, store-branded password reset that ends other sessions, guest-cart merge on sign-in, a read-only admin orders list, daily pruning of abandoned carts, and checkout and account pages kept out of search engines.

## Next
Online payments (M-Pesa), billing, store vetting, sales reports, launch.
