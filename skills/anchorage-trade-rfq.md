---
name: Execute a trade via RFQ
description: Request a quote, accept it, and confirm the resulting order and trade on the Anchorage Digital trading API.
api: openapi/anchorage-v2-openapi-original.yml
operations: [getTradePairs, requestQuote, acceptQuote, getOrderStatus, getTrade]
---

# Execute a trade via RFQ

Trade digital assets using Anchorage Digital's request-for-quote flow.

## Auth
- `Api-Access-Key` header; sign order-placing calls with Ed25519.
- Requires **Execute trades** (read-only steps accept **Read trade activity**).

## Steps
1. **Check tradable pairs** — `getTradePairs` (GET `/trading/pairs`).
2. **Request a quote** — `requestQuote` (POST `/trading/quote`) for the pair and size.
3. **Accept the quote** — `acceptQuote` (POST `/trading/quote/accept`) before it expires (a `QuoteExpired` error means re-quote).
4. **Confirm** — `getOrderStatus` (GET `/trading/orders/{orderId}`) then `getTrade` (GET `/trading/trades/{tradeId}`) to read the fill and settlement.

## Notes
- For fire-and-forget flow use `newOrderSingle` (POST `/trading/order`) or `postAsyncOrder` / `cancelAsyncOrder`.
- Real-time market data and order submission are also available over the trading WebSocket.
- Errors: `{errorType, message}`; watch for `QuoteExpired`, `InsufficientFunds`, `Forbidden`.
