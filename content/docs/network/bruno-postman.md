---
weight: 7030
title: "Bruno and Postman"
description: "Two GUI clients for saving API requests as collections and sharing them."
icon: "folder_shared"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Once you've outgrown pasting curl commands into Slack, you need somewhere to keep a set
of requests and swap environments. There are two broad choices.

## What differs

| | Bruno | Postman |
|---|---|---|
| Storage | Files (.bru) managed in Git | Cloud workspace |
| Account | Not required | Required for collaboration |
| Collaboration | Branches and PRs as usual | Web-based sharing and permissions |
| Breadth | Focused on the core | Monitors, mock servers, documentation |
| Best when | Requests live beside the code | A whole organisation, including non-engineers |

Choose Bruno if you want requests committed alongside the code, Postman if you need an
API portal for the wider company.

## Getting started with Bruno

```bash
brew install --cask bruno
```

Creating a collection produces folders and `.bru` files. Commit them.

```
api/
  bruno.json
  environments/
    local.bru
    prod.bru
  users/
    list-users.bru
    create-user.bru
```

`.bru` files are text, so diffs are readable.

```
meta {
  name: Create user
  type: http
}

post {
  url: {{baseUrl}}/users
  body: json
}

headers {
  Authorization: Bearer {{token}}
}

body:json {
  {
    "name": "Alex Kim",
    "email": "alex@example.com"
  }
}
```

## Environment variables

Moving between local, staging, and production only changes the URL and the token. Split
those into environment files.

```
vars {
  baseUrl: https://api.example.com
}
vars:secret [
  token
]
```

Anything under `vars:secret` is not written to the file, so tokens never get committed.
Never put a production token in an ordinary variable.

## Adding tests

Each request can carry a script that validates the response.

```javascript
test("returns 201", function() {
  expect(res.getStatus()).to.equal(201);
});

test("has an id", function() {
  expect(res.getBody().id).to.be.a("string");
});
```

Run the whole collection from the CLI and it works in CI too.

```bash
npm install -g @usebruno/cli
bru run --env local
```

Postman's equivalent is `newman`.

```bash
npm install -g newman
newman run collection.json -e local-env.json
```

## Migrating

Export a Postman collection and import it into Bruno. Both also accept a pasted curl
command and turn it into a request.

## Next

To expose a locally built API for a moment →
[Exposing a Local Server](/docs/network/tunneling/)
