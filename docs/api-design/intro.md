---
sidebar_position: 1
description: Getting started with the Turnkey API
slug: /api-introduction
---

# Introduction

## REST/HTTP

Turnkey's Public API is a REST API. We chose REST-over-HTTP for convenience and ease-of-use: most of our customers should be able to integrate with Turnkey's public API without a major re-architecture of their existing systems.

Many client libraries are available to make requests to a REST/HTTP API, across many languages. Turnkey will provide SDKs for the most popular programming languages. For other languages, a REST/HTTP API ensures there is an easy integration path available via raw http clients.

## POST-only

If you look at our [API reference](./api) you'll notice that Turnkey is not strictly following the REST convention:

We use POST exclusively
We do not have full resource paths in the URL (for example, "/submit/sign")
This decision comes from a security guarantee we're making to our customers: requests must be signed by end-users, and verified by Turnkey's secure enclaves. Unlike other companies, we do not modify your request while in transit. This ensures cryptographic integrity, end-to-end: what is signed with your API key is what is seen and checked by our secure enclaves.

So why is a POST-only API a better fit? That's because of signatures. With GET requests, the URL and URL params need to be signed. With POST requests, the body and URL need to be signed.

By switching to a POST-only API and moving all critical request parameters to the body, we simplify the definition of what has to be signed with each request: it's simply the POST body. Read the next section for more details on API signatures.

## API signatures and X-Stamp header

Each request made to Turnkey has to have an X-Stamp header attached to it. This signature is checked in our secure enclave applications. The scheme to sign is as follows:

1. SHA256 hash a JSON-encoded string of your request's body
2. Sign that hash with either your api key or webauthn credential
3. Create a JSON-encoded string with the appropriate properties depending on authenticator type:
    - If using an Api Key, make a JSON-encoded string with public key, signature, and signature scheme
    - If using an WebAuthn Credential, make a JSON-encoded string with the authenticator data, client data, credential ID and signature
4. Base64url encode this string
5. Add this string to your request as an X-Stamp header

In practice you should not have to worry about this step: our [JS SDK](https://github.com/tkhq/sdk) and [CLI](https://github.com/tkhq/tkcli) will take care of it for you. If you write an independent client however, you'll have to implement this yourself.

For reference, here is how we've implemented this:

- in our CLI: [apikey.go](https://github.com/tkhq/tkcli/blob/7f0159af5a73387ff050647180d1db4d3a3aa033/src/internal/apikey/apikey.go#L146-L166)
- in our SDK's Api Key stamper: [index.ts](https://github.com/tkhq/sdk/blob/main/packages/api-key-stamper/src/index.ts#L41)
- in our SDK's WebAuthn stamper: [index.ts](https://github.com/tkhq/sdk/blob/main/packages/webauthn-stamper/src/index.ts#L39)

## Queries and Submissions
Our API endpoints are divided in 2 broad categories: queries and submissions.

- Queries are read requests (for example: "get users")
- Submissions are requests to execute an activity on Turnkey (for example: "create policy")

We've separated these 2 categories because all submissions return an Activity and can be subject to consensus if your organization has consensus-based policies. It's best to think of a call to "/submit/..." as a request to start an asynchronous process. The response to a submit request will contain new activity, with an ID you can use to poll until activity completion if needed.

Our API is idempotent: for each activity submission, the whole request body is hashed into a fingerprint. If you resubmit the same payload, you'll get the same activity instead of a new one. This helps with idempotency, and helps with security too. Because each activity request must contain a recent timestamp (in the `timestampMs` field), this avoids replay attacks: a third party can't generate new activities by re-POSTing previous activity requests.

Queries return data synchronously, do not generate activities, and are not subject to consensus.

