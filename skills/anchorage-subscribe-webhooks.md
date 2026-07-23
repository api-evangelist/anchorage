---
name: Subscribe to webhook events
description: Register a webhook endpoint, subscribe it to event types, and validate signed deliveries on the Anchorage Digital API.
api: openapi/anchorage-v2-openapi-original.yml
operations: [listWebhookEventTypes, createWebhookEndpoints, createWebhookEndpointSubscriptions, getWebhookValidationKey, listWebhookEndpointSubscriptions]
---

# Subscribe to webhook events

Receive event-driven notifications instead of polling.

## Auth
- `Api-Access-Key` header; requires the **Configure Webhooks** permission.

## Steps
1. **Discover event types** — `listWebhookEventTypes` (GET `/webhook/event-types`); each has an `id` (e.g. `transfer.completed`) and `description`.
2. **Register an endpoint** — `createWebhookEndpoints` (POST `/webhook/endpoints`) with your HTTPS callback URL.
3. **Subscribe** — `createWebhookEndpointSubscriptions` (POST `/webhook/endpoints/{endpointId}/subscriptions`) for the event ids you want; review with `listWebhookEndpointSubscriptions`.
4. **Validate deliveries** — fetch the signing key via `getWebhookValidationKey` (GET `/webhook/validation-key`) and verify every payload signature before acting on it.

## Notes
- Respond within the documented timeout; deliveries are retried (see `asyncapi/anchorage-webhooks.yml`).
- Errors: `{errorType, message}`.
