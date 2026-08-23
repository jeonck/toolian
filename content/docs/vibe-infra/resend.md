---
weight: 11060
title: "Resend"
description: "Transactional email with an API you can call in one line — and the DNS records that decide whether it lands in the inbox."
icon: "mail"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

Sign-up confirmations, password resets, receipts, the alert you send yourself: every app
sends email, and doing it from your own SMTP server means fighting spam filters forever.
Resend is a send API built for developers — a few lines to send, React components for
templates, and delivery logs you can actually read.

## Free tier

3,000 emails per month, capped at **100 per day**, plus 1,000 marketing contacts. Not a
trial. The daily cap is the one to design around: a batch that mails every user at once
will hit it long before the monthly number.

## Send your first email

```bash
npm install resend
```

```js
import { Resend } from "resend";

const resend = new Resend(process.env.RESEND_API_KEY);

await resend.emails.send({
  from: "onboarding@resend.dev",       // testing only — see below
  to: "you@example.com",
  subject: "It works",
  html: "<p>Hello from the app.</p>",
});
```

`onboarding@resend.dev` sends without any setup, but only **to the address you signed up
with**. It's for proving the wiring, nothing more.

## Verify a domain, or nothing lands

To send as `hello@yourdomain.com`, add the domain in the dashboard and copy the records
it gives you into your DNS:

| Record | What it does |
|---|---|
| `MX` + `TXT` on a subdomain | SPF — says Resend may send for you |
| `TXT` (DKIM) | Signs the message so it can't be forged |
| `TXT` (DMARC) | Tells receivers what to do with mail that fails the first two |

Verification takes minutes to a few hours. Skipping DMARC is the usual reason mail
reaches Gmail's spam folder while your own tests looked fine.

Send from a subdomain — `mail.yourdomain.com` — so that a reputation problem with
automated mail never touches your personal address on the apex domain.

## Templates as components

If you're already in React, write the email as one:

```bash
npm install @react-email/components
```

```jsx
export function Welcome({ name }) {
  return (
    <Html>
      <Text>Hi {name}, thanks for signing up.</Text>
      <Button href="https://example.com/start">Get started</Button>
    </Html>
  );
}
```

```js
await resend.emails.send({
  from: "hello@mail.yourdomain.com",
  to: user.email,
  subject: "Welcome",
  react: <Welcome name={user.name} />,
});
```

Email HTML is a decade behind the web — tables, inline styles, no flexbox worth relying
on. Letting the library render it is the point.

## Webhooks for what happened next

Point a webhook at your app and Resend posts `email.delivered`, `email.bounced`, and
`email.complained` events. Store bounces and stop sending to those addresses: a list
full of dead addresses is how a sending domain loses its reputation.

## Habits worth keeping

- **Never send from a request handler.** A slow provider becomes a slow sign-up. Queue
  it, or send it after you've responded.
- **Make sends idempotent.** A retried webhook that re-sends a receipt is a support
  ticket.
- **Keep a `RESEND_API_KEY` per environment** so a staging test can't mail real users.
- **Log the returned id.** It's how you find one message among thousands in the
  dashboard when a customer says nothing arrived.

## Next

When something breaks, you want to hear it from a tool, not a user →
[Sentry](/docs/vibe-infra/sentry/)
