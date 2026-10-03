# ShoppingMall — showcase

> **In active development, not launched.** The source is private. This repo is my engineering notes on it: the decisions I made, what I got wrong, and my progress. Figures are as of 30 Sep 2026.

ShoppingMall is a multi-merchant commerce platform for Kenya. A merchant registers, creates a store and gets a storefront at their own handle (custom domains later). I'm building it solo on Laravel 13, Livewire 4, PostgreSQL and Redis, with a strong bias towards tests and static analysis.

## Decisions I made, and why

**1. Single-database multi-tenancy, but tenancy isn't initialised everywhere.**
A store is the tenant, scoped by a `store_id` column. On the admin side I resolve the store through ordinary route-model binding and don't boot the tenancy package at all, because the admin surface needs the store as plain input, not as a globally swapped context. Tenancy is initialised only for the public storefront (by hostname) and for background jobs. The cost is two ways of identifying a store, so the rule that makes it safe is that every Action takes its `Store` as an explicit parameter and never reads ambient tenant state. The hostname table is the one deliberately unscoped model, because the host has to be resolved before any tenant exists.

**2. One writer for stock.**
Only one Action may change inventory quantities. It wraps the read-modify-write in a transaction with a row lock, and rejects negative results and cross-store pairings before writing. An architecture test stops the web layer from querying those models directly. *What I got wrong first:* my early tests ran on SQLite, where row locks do nothing, so the race protection was only verified by reading framework source. I added a Postgres-only test group that checks the `select … for update` really happens before the stock update.

**3. Storage isolation by construction, not by discipline.**
Call sites receive an already-prefixed disk, so there is no "forgot to add the store prefix" step to get wrong. The isolation test proves it with a real negative existence check (the file must not exist under the other store), not by assumption, so it fails if isolation breaks.

**4. Merchant-authored content is treated as hostile.**
Anything a merchant types reaches the page through one sanctioned path per kind: markdown goes through a single renderer that strips raw HTML, URLs through a single validating rule, and theme settings are whitelisted before they can become CSS. Everything else is escaped.

**5. A bug I found while building.**
Signed draft-preview links were signed over the path only, so a link signed for one store could be replayed on another store's host to mint a preview cookie. I bound the signature to the store, added a check against the store the request resolves to, and wrote the test. It was found and fixed while building, before anything shipped.

**6. Strict by default, with budgets.**
Eloquent strict mode and lazy-load prevention outside production, and query-count tests on the endpoints that matter (the storefront cart asserts zero queries without a token and exactly one with one; menus have a query budget).

## Where I'm at
Built: store onboarding and staff, platform admin, catalogue with per-location inventory, themed storefront with a draft-preview-publish theme editor, carts with live pricing, cash-on-delivery checkout, store-scoped customer accounts, and an admin orders list. Not built: online payments (M-Pesa), billing, store vetting, sales reports, launch. See the [dev log](DEVLOG.md).

**Numbers:** 169 Pest test files (about 716 tests), PHPStan level 7 with the tests included, Rector and Pint in CI, 24 models, roughly 28k lines of PHP.

## Why the code is private
It's a commercial product in development. I'm happy to walk through any part of it in an interview, or to give named interviewers read-only access.

[LinkedIn](https://linkedin.com/in/jeremymleo) · jeremymleo@gmail.com
