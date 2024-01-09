---
sidebar_position: 7
description: Learn about Wallets on Turnkey
slug: /getting-started/wallets
---
# Wallets

A [hierarchical deterministic (HD) Wallet](https://learnmeabitcoin.com/technical/hd-wallets) is a collection of cryptographic private/public key pairs that share a common seed. A Wallet is used to generate Accounts.

```json
{
   "walletId": "eb98ae4c-07eb-4117-9b2d-8a453c0e1e64",
   "walletName": "default"
}
```

## Accounts

An account contains the directions for deriving a cryptographic key pair and corresponding address from a Wallet. In practice, this looks like:
- The Wallet seed and Account curve are used to create a root key pair
- The Account path format and path are used to derive an extended key pair from the root key pair
- The Account address format is used to derive the address from the extended public key

```json
{
   "address": "0x7aAE6F67798D1Ea0b8bFB5b64231B2f12049DB5e",
   "addressFormat": "ADDRESS_FORMAT_ETHEREUM",
   "curve": "CURVE_SECP256K1",
   "path": "m/44'/60'/0'/0/0",
   "pathFormat": "PATH_FORMAT_BIP32",
   "walletId": "eb98ae4c-07eb-4117-9b2d-8a453c0e1e64"
}
```

**The account address is used to sign with the underlying extended private key.**

#### Configuration

Certain address formats can only be used with particular curves. See the table below:

| Type           | Address Format               | Curve           | Path Format       | Standard Path     |
| -------------- | ---------------------------- | --------------- | ----------------- | ----------------- |
| **Public Key** | ADDRESS_FORMAT_COMPRESSED    | all             | PATH_FORMAT_BIP32 | none              |
|                | ADDRESS_FORMAT_UNCOMPRESSED  | CURVE_SECP256K1 | PATH_FORMAT_BIP32 | none              |
| **Ethereum**   | ADDRESS_FORMAT_ETHEREUM      | CURVE_SECP256K1 | PATH_FORMAT_BIP32 | m/44'/60'/0'/0/0  |
| **Cosmos**     | ADDRESS_FORMAT_COSMOS        | CURVE_SECP256K1 | PATH_FORMAT_BIP32 | m/44'/118'/0'/0/0 |
| **Solana**     | ADDRESS_FORMAT_SOLANA        | CURVE_ED25519   | PATH_FORMAT_BIP32 | m/44'/501'/0'/0'  |

#### What if I don't see the address format for my network?
You can use `ADDRESS_FORMAT_COMPRESSED` to generate a public key which can be used to sign with.

#### What if I don't see the curve for my network?
Contact us at hello@turnkey.com.

## Private Keys

Turnkey also supports raw private keys, but we recommend using Wallets since they offer several advantages:
- Wallets can be used across various cryptographic curves
- Wallets can generate millions of addresses for various digital assets
- Wallets can be represented by a checksummed, mnemonic phrase making them easier to backup and recover

## Export keys

Exporting on Turnkey enables you or your end users to export a copy of a Wallet or Private Key from our system at any time. While most Turnkey users opt to keep Wallets within Turnkey's secure infrastructure, the export functionality means you are never locked into Turnkey, and gives you the freedom to design your own backup processes as you see fit. Check out our [Export Wallet guide](../integration-guides/export-wallets.md) to allow your users to securely export their wallets.
