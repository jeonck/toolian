---
weight: 11100
title: "Clerk"
description: "Sign-in, sessions, and user management as a drop-in — and the reasons not to write auth yourself."
icon: "lock_person"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Authentication looks like a weekend of work and isn't. Password hashing, sessions,
refresh, email verification, password reset, OAuth for four providers, MFA, account
linking, rate limiting on the login route, and a support burden forever — and every one
of those is a place where a mistake is a breach rather than a bug.

[Clerk](https://clerk.com/) is the drop-in version: prebuilt components, a hosted user
database, and session handling that works with the frameworks you're already using.

## Add it

```bash
npm install @clerk/nextjs
```

```tsx
// app/layout.tsx
import { ClerkProvider, SignInButton, UserButton, SignedIn, SignedOut } from '@clerk/nextjs';

export default function RootLayout({ children }) {
  return (
    <ClerkProvider>
      <html><body>
        <header>
          <SignedOut><SignInButton /></SignedOut>
          <SignedIn><UserButton /></SignedIn>
        </header>
        {children}
      </body></html>
    </ClerkProvider>
  );
}
```

```ts
// server side
import { auth } from '@clerk/nextjs/server';

const { userId } = await auth();
if (!userId) return new Response('Unauthorized', { status: 401 });
```

That's sign-in, sign-up, a session, a profile menu, and a server-side check. Social
providers, email codes, and passkeys are toggles in the dashboard rather than code.

## What you get beyond a login box

- **Organisations** — multi-tenancy with invitations, roles, and an org switcher. This is
  the feature that saves the most time in a B2B product, and the one people underestimate.
- **Session management** — device list, revoke, and sensible refresh behaviour.
- **A user database with a UI** your support people can use without a SQL client.
- **Webhooks** — `user.created` and friends, so your own database can keep a row per user
  and everything else keys off your ID.

That last one is the integration pattern worth following: Clerk owns identity, your
database owns the user's data, and a webhook keeps them joined. Never scatter Clerk user
IDs through your schema without a local users table to hang them on.

## Cost, and the unit to read carefully

The free tier is generous — tens of thousands of users, raised again in early 2026 — but
Clerk bills **monthly retained users**, a narrower unit than the monthly active users most
competitors meter. Model your own numbers against that definition rather than assuming
they match, and re-check before a launch that could multiply signups.

## The alternatives, honestly

| Option | Fits |
|---|---|
| **Clerk** | React/Next apps that want components and organisations, fast |
| **Supabase Auth** | Already on [Supabase](/docs/vibe-infra/supabase/) — it's included and integrates with RLS |
| **Better Auth / Auth.js** | Open source, in your own database, no per-user bill, more assembly |
| **Auth0 / Cognito / Entra** | Enterprise SSO, SAML, compliance checklists |
| **Roll your own** | Almost never — and if you must, use a vetted library, never your own crypto |

The honest summary: if you're already on Supabase, use its auth. If you want to own the
data and avoid per-user pricing, use an open-source library. If you want to be done this
afternoon and organisations matter, Clerk is the shortest path.

Whatever you pick, **do not store passwords yourself** unless securing them is your
product.

## Next

Every app has to send email eventually → [Resend](/docs/vibe-infra/resend/)
