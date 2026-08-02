---
name: switchfly-refund-booking
description: Cancel a Switchfly booking (or individual components) using a machine-to-machine admin token.
api: Switchfly Loyalty API
operations:
  - ClientCredentialAdminToken
  - Refund
---

# Refund / Cancel a Switchfly Booking

Cancel a whole booking or specific components and refund the traveler.

## Steps

1. **Get an admin token.** Call `ClientCredentialAdminToken` (`POST /admin/token`)
   with the OAuth 2 `client_credentials` grant (`client_id` + `client_secret`) to
   obtain a machine-to-machine bearer token (scheme `adminBearerAuth`).
2. **Refund.** Call `Refund` (`POST /refund`) with the booking reference and the
   components to cancel. This is a balance-mutating action — confirm intent first.

## Rules & error handling

- `400` = malformed request: validate the refund body.
- `401` = admin token missing/expired: re-run `ClientCredentialAdminToken`.
- `403` = client not permitted for the channel.
- Refund is an explicit cancel action, not an idempotent retry — check the prior
  `RedemptionConfirmation`/booking state before re-issuing. See
  `errors/switchfly-problem-types.yml`.
