---
title: Firebase Secrets
description: How to handle secrets with Firebase
date: 2021-10-07
tags:
  - Firebase
  - Environment
  - Secrets
  - Serverless
  - Web
layout: layouts/post.njk
---

Outline
- What are secrets?
- How NOT to store secrets on web
  - Storing secrets on client is a terrible idea.
  - Encrypted client-side secrets
  - Pulling secrets from remote database to the client
- The secrets should never leave the server
- How to store secrets with Firebase

Resources:
- https://www.androidauthority.com/where-is-the-best-place-to-store-a-password-in-your-android-app-597197/
- https://medium.com/poka-techblog/the-best-way-to-store-secrets-in-your-app-is-not-to-store-secrets-in-your-app-308a6807d3ed
- https://github.com/daylen/api-key-detect/blob/master/api_key_detect.py

# 🥸 What are secrets

In case you ever needed to access a 3rd party service from your front-end web application, you likely stumbled upon terms like "environment variable" or it's more specific sibling, a "secret".

The "secrets" refers to a subset of environment variables that should never be stored on client-side such as passwords, API keys, authentication tokens or other various credentials.

This is _twice_ as important if we're talking about web.

Regardless of platform though, hijacking such a secret by an attacker could lead to a security breach.

# ❌ How NOT to store secrets

As with the majority of things, there are more ways do it wrong than there are ways to do it right.

Not surprisingly, this applies also to managing secrets.

### Plain-text secrets on client is a terrible idea.

By this I mean storing plain-text secrets in your Javascript code as variable or constants.

On page load, anyone can browse through the page's assets and waltz right into your source code.

Even though, obfuscation of Javascript is a standard practice, there is nothing easier than grabbing a single script that crawls the code-base and looks for secrets by a pattern.

### Encrypted client-side secrets are dead end.

Encrypting client-side secrets (still variables or constants) may look promising, until you have to resolve where to store the encryption key (secret) to unlock the values.

This leaves us stuck with the same problem.

### Likewise for pulling secrets from a remote DB to the client.

Similar scenario will occur in case we try to store the secrets in a remote database and pull it to the client.

We need authentication credentials to access the database itself, which need to be stored in a secure way. Same thing for the secret we'd be fetching. The secret still needs to be somehow encrypted, otherwise it's trivial to hijack it from the incoming response.

# ✅ The secrets should never leave the server

TODO
