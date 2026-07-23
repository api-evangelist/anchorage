---
name: Withdraw crypto with quorum approval
description: Move digital assets out of an Anchorage Digital wallet to a trusted destination via a quorum-approved withdrawal, and track it to completion.
api: openapi/anchorage-v2-openapi-original.yml
operations: [getWallets, createWithdrawal, getTransaction, getTransactions]
---

# Withdraw crypto with quorum approval

Withdrawals move assets out of a wallet and require quorum approval on every request.

## Auth
- `Api-Access-Key` header on every request.
- `createWithdrawal` is a sensitive operation: sign it with Ed25519 (`Api-Signature`, `Api-Timestamp`) and include an `idempotentId` (UUID) so retries are safe.
- Requires the **Initiate withdrawals** permission; the destination must already be an allowlisted trusted destination.

## Steps
1. **Pick the source wallet** — `getWallets` (GET `/wallets`) to resolve the `walletId`.
2. **Create the withdrawal** — `createWithdrawal` (POST `/transactions/withdrawal`) with amount, asset, destination, and a unique `idempotentId`. This enters quorum approval.
3. **Track status** — poll `getTransaction` (GET `/transactions/{transactionId}`) or list with `getTransactions` until the transaction reaches a terminal state, or subscribe to webhooks.

## Conventions
- Idempotency: reuse the same `idempotentId` on retries to avoid double-spends (`conventions/anchorage-conventions.yml`).
- Transfers (`createTransfer`) are the lighter-weight sibling for moving funds to pre-approved trusted destinations without per-transaction quorum.
- Errors: `{errorType, message}`; `InsufficientFunds` and `Forbidden` are common here.
