---
weight: 11080
title: "Lemon Squeezy"
description: "Taking money from anywhere in the world without registering for tax anywhere — what a merchant of record buys you, and what it costs."
icon: "credit_card"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

The hard part of selling software internationally isn't the card form. It's that a
customer in Germany owes VAT at their rate, one in California owes state sales tax, and
somebody has to register, collect, and remit in each jurisdiction. A **merchant of
record** takes that on: Lemon Squeezy is the legal seller, your customer's invoice comes
from them, and the tax problem is theirs.

You get paid a payout, not a transaction — and you never file a VAT return in a country
you've never visited.

## What it costs

Nothing until you sell. Then:

| Charge | Rate |
|---|---|
| Base | 5% + $0.50 per transaction |
| International cards | +1.5% |
| PayPal | +1.5% |
| Subscriptions | +0.5% |

Roughly 2% more than raw Stripe, and that 2% is what you'd otherwise spend on tax
compliance — or on the accountant who tells you which registrations you were supposed to
have.

**Worth knowing:** Stripe acquired Lemon Squeezy in 2024, and Stripe now sells its own
merchant-of-record product at the same headline rate. Lemon Squeezy still operates and
still onboards, but expect verification to take days rather than hours, and check the
current terms before you build around it.

## The five-minute version

For a lot of products you need no code at all:

1. Create a store, add a **product**, set a price (one-off or subscription).
2. Attach the file, licence key, or redirect URL the buyer should receive.
3. Copy the **checkout link** and put it behind your buy button.

Lemon Squeezy hosts the checkout, charges the card, emails the receipt, and delivers the
download. For an ebook, a template, or a desktop app licence, that is the entire
integration.

## Unlocking features in your own app

When the purchase has to flip a flag on an account, pass your user's identity into the
checkout and listen for the webhook coming back.

```
https://yourstore.lemonsqueezy.com/buy/<variant-id>?checkout[custom][user_id]=42
```

Then verify and handle the event server-side. **Verify the signature** — the endpoint is
public, and an unsigned handler is a free-subscription button:

```js
import crypto from "node:crypto";

export async function POST(req) {
  const raw = await req.text();
  const digest = crypto
    .createHmac("sha256", process.env.LEMONSQUEEZY_WEBHOOK_SECRET)
    .update(raw)
    .digest("hex");

  const signature = req.headers.get("x-signature");
  const ok =
    signature &&
    crypto.timingSafeEqual(Buffer.from(digest), Buffer.from(signature));
  if (!ok) return new Response("bad signature", { status: 401 });

  const event = JSON.parse(raw);
  const userId = event.meta.custom_data?.user_id;

  switch (event.meta.event_name) {
    case "order_created":
    case "subscription_created":
    case "subscription_updated":
      await grantAccess(userId, event.data);
      break;
    case "subscription_expired":
      await revokeAccess(userId);
      break;
  }

  return new Response("ok");
}
```

Three things this handler has to get right, and they're the same three everywhere:

- **Verify the signature** before parsing anything as truth.
- **Be idempotent.** Webhooks retry; granting access twice must be harmless.
- **Return 200 quickly.** Do the slow work after responding, or the retries pile up.

Subscribe to the cancellation and expiry events on day one. Access that is never revoked
is a bug you only discover in your revenue numbers months later.

## Test before you launch

Test mode issues separate keys and accepts test cards, and the dashboard replays webhook
deliveries with their responses — the fastest way to find out that your handler 500s on
a field you didn't expect.

## Before you build on it

Merchant-of-record platforms are strict about what they'll sell, and they hold payouts
while they verify a new store. Read the acceptable-use terms and start verification
before launch day, not on it.

## Next

Selling to customers in Korea → [Groble](/docs/vibe-infra/groble/)
