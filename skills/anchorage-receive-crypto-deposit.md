---
name: Receive a crypto deposit
description: Create a wallet in a vault, provision a verified deposit address, and track the inbound deposit to completion on the Anchorage Digital API.
api: openapi/anchorage-v2-openapi-original.yml
operations: [getVaults, createVaultWallet, provisionWalletAddress, getAddresses, getTransactions]
---

# Receive a crypto deposit

Use the Anchorage Digital REST API v2.0 to take custody of an inbound crypto deposit.

## Auth
- Send `Api-Access-Key: <your key>` on every request.
- Base URL: `https://api.anchorage.com/v2` (sandbox: `https://api.anchorage-staging.com/v2`).
- State-changing calls below also require an Ed25519 signature (`Api-Signature`, `Api-Timestamp`). See `authentication/anchorage-authentication.yml`.

## Steps
1. **Find the vault** — `getVaults` (GET `/vaults`) to pick the `vaultId` that will hold the asset. Requires **Read vault activity**.
2. **Create the wallet** — `createVaultWallet` (POST `/vaults/{vaultId}/wallets`) for the target asset/networks. Requires **Create address**.
3. **Provision an address** — `provisionWalletAddress` (POST `/wallets/{walletId}/addresses`). Verify the returned address with Anchorage's V1/V2 address signature scheme before sharing it. Requires **Create address**.
4. **Share & watch** — list addresses with `getAddresses` (GET `/vaults/{vaultId}/addresses`), then poll `getTransactions` (GET `/transactions`) for the inbound deposit. Prefer webhooks over polling (see the webhook-subscription skill).

## Conventions
- Pagination is cursor-based: pass `afterId`/`limit`, follow `page.next` (see `conventions/anchorage-conventions.yml`).
- Errors return `{errorType, message}` (see `errors/anchorage-problem-types.yml`).
- Mainnet addresses cannot be created in the sandbox.
