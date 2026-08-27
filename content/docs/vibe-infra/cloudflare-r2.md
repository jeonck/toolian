---
weight: 11090
title: "Cloudflare R2"
description: "S3-compatible object storage with no egress fees — the right home for uploads, images, and backups."
icon: "cloud_upload"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

User uploads, generated images, PDFs, database dumps: none of them belong in Postgres,
and serving them from your app container is how a $5 host becomes a $50 one. R2 is
object storage that speaks the S3 API, so every existing SDK and tool works — and it
charges **nothing for egress**, at any volume, on any plan.

That last part is the whole argument. On S3 the bill scales with how popular your files
are; on R2 it scales with how many you keep.

## Free tier

| Included per month | Amount |
|---|---|
| Storage | 10 GB |
| Class A operations (writes, lists) | 1 million |
| Class B operations (reads) | 10 million |
| Egress | Unlimited, free, forever |

No expiry and no trial conversion. Past 10 GB it's about $0.015 per GB-month, and egress
stays free.

## Create a bucket

Cloudflare dashboard → **R2 → Create bucket**, then **Manage R2 API Tokens** for an
access key pair. Or from the CLI:

```bash
npm install -g wrangler
wrangler login
wrangler r2 bucket create my-app-uploads
wrangler r2 object put my-app-uploads/hello.txt --file ./hello.txt
```

The S3 endpoint is `https://<account-id>.r2.cloudflarestorage.com`, and the region string
is `auto`.

```bash
aws s3 ls s3://my-app-uploads \
  --endpoint-url https://<account-id>.r2.cloudflarestorage.com
```

## Upload from your app

Do not proxy file bytes through your own server — that burns the compute you're paying
for and caps you at the host's request size limit. Instead have the server sign a URL and
let the browser upload straight to R2.

```bash
npm install @aws-sdk/client-s3 @aws-sdk/s3-request-presigner
```

```js
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";
import { getSignedUrl } from "@aws-sdk/s3-request-presigner";

const r2 = new S3Client({
  region: "auto",
  endpoint: `https://${process.env.R2_ACCOUNT_ID}.r2.cloudflarestorage.com`,
  credentials: {
    accessKeyId: process.env.R2_ACCESS_KEY_ID,
    secretAccessKey: process.env.R2_SECRET_ACCESS_KEY,
  },
});

// server: hand the browser a URL that is valid for five minutes
export async function uploadUrl(key, contentType) {
  return getSignedUrl(
    r2,
    new PutObjectCommand({ Bucket: "my-app-uploads", Key: key, ContentType: contentType }),
    { expiresIn: 300 },
  );
}
```

The browser then `PUT`s the file to that URL. Your server only ever handles the key, and
the key is what you store in Postgres — never the file itself.

## Serving files back

A bucket is private by default, which is the correct default. Two ways out:

- **Public bucket on a custom domain.** Connect a domain you have on Cloudflare
  (**Settings → Public access**) and the CDN caches it worldwide. Right for avatars,
  product images, anything not secret.
- **Presigned `GetObject` URLs.** Same signing code with `GetObjectCommand`, a short
  expiry. Right for anything a customer paid for.

Skip `r2.dev` for production traffic — it's rate limited and meant for testing.

## Housekeeping

- **Name keys by their owner:** `uploads/{userId}/{uuid}-{filename}`. Listing and
  deleting a user's data later becomes trivial.
- **Set a lifecycle rule** to expire temporary prefixes automatically; 10 GB fills faster
  than you expect once thumbnails and old dumps pile up.
- **Never ship the secret key to the browser.** Presigned URLs exist precisely so you
  don't have to.
- **Keep backups here too** — `pg_dump` output plus free egress means restoring costs
  nothing but time.

## Next

Before anyone can own a file, they have to sign in → [Clerk](/docs/vibe-infra/clerk/)
