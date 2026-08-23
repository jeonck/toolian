---
weight: 11090
title: "Groble"
description: "Selling a digital product to Korean customers — KakaoPay and NaverPay included, no business registration required."
icon: "storefront"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

International checkouts lose Korean customers. The payment method they expect is
KakaoPay or NaverPay, the receipt has to look like a Korean receipt, and the domestic
gateways traditionally want a 사업자등록증 (business registration) and a contract before
they'll talk to you — a wall in front of anyone who just wants to sell one ebook.

[Groble](https://www.groble.im/) is the domestic answer: a hosted storefront for digital
products where **an individual with no business registration can start selling
immediately**, and the platform handles the checkout, the receipt, and the settlement.

## What it costs

Free to set up and list; the fee comes out of each sale.

| Sale type | Fee |
|---|---|
| One-off payment | 4.4% |
| Recurring subscription | 5.4% |

VAT is charged on the fee separately. Settlement runs four times a month — on the 1st,
8th, 16th, and 24th — with file-based digital products settling on a shorter cycle than
services.

Cheaper than an international merchant of record, because the tax surface is one country
rather than fifty.

## What it fits

Groble is built around knowledge products: ebooks and PDFs, Notion and PPT templates,
recorded courses, consulting and coaching sessions, commissioned work. Payment methods
cover cards plus the simple-pay wallets Korean buyers actually use, and recurring
subscriptions are supported for anything sold monthly.

What it is **not** is a payments API for a SaaS product. There's no documented developer
integration to build a metered billing flow on — it's a storefront with a link.

## Setting up

1. Sign up and create the seller profile; supply the settlement bank account.
2. Add a product — title, description, price, and the file or booking details the buyer
   receives.
3. Publish, and share the product link from your blog, Threads, or newsletter.

The link is the integration. Buyers pay on Groble's page and get the file or the booking
confirmation from Groble; you watch orders in the dashboard.

## Wiring it to your own app

Since there's no webhook to hook a feature flag to, match purchases by the buyer's email
instead: export or read the order list, and grant access on that address. For a small
product this is a five-minute daily chore, and it stays honest — one place holds the
truth about who paid.

If access has to be instant and automatic, that's the signal to move to a platform with
webhooks: [Lemon Squeezy](/docs/vibe-infra/lemon-squeezy/) internationally, or a Korean
PG with an API once you have the business registration that requires.

## Two obligations that stay yours

- **Income is still income.** The platform handles the transaction and settles to you;
  declaring what you earned is your side of it. Selling without a business registration
  is fine to start with, but past a certain volume registering is the correct move —
  worth an hour with an accountant rather than a guess.
- **The refund policy is a legal document.** Korean e-commerce rules treat digital
  content differently once it's been downloaded. Write the policy before the first sale,
  not after the first dispute.

## Choosing between the two payment pages

| | [Lemon Squeezy](/docs/vibe-infra/lemon-squeezy/) | Groble |
|---|---|---|
| Customers | Global | Korea |
| Fee | 5% + $0.50, add-ons on top | 4.4% (5.4% recurring) + VAT |
| Payment methods | Cards, PayPal | Cards, KakaoPay, NaverPay |
| Automation | Webhooks, checkout API | Link only |
| Requirements | Store verification | None to start |

Nothing stops you running both — the same PDF sold through Groble at home and Lemon
Squeezy everywhere else.

## Next

One category left, on the assets a project needs and has no budget for →
[AI Media Tools](/docs/ai-media/)
