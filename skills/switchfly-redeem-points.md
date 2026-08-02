---
name: switchfly-redeem-points
description: Redeem loyalty points for a Switchfly traveler using OAuth 2 SSO, then read back the confirmation.
api: Switchfly Loyalty API
operations:
  - TokenRequest
  - TravelerProfile
  - Redeem
---

# Redeem Switchfly Loyalty Points

Redeem points from a traveler's loyalty account against a booking.

## Steps

1. **Authenticate.** Call `TokenRequest` (`POST /token`) with the OAuth 2
   `authorization_code` (with PKCE) or `client_credentials` grant to obtain a
   bearer token. Send it as `Authorization: Bearer <token>` on subsequent calls
   (scheme `bearerAuth`).
2. **(Optional) Load the profile.** Call `TravelerProfile` (`GET /traveler-profile`)
   to confirm the member and their available points balance before spending.
3. **Redeem.** Call `Redeem` (`POST /redeem`) with the redemption body. This is a
   balance-mutating action — confirm intent before calling. On success you receive
   a `RedemptionConfirmation`.

## Rules & error handling

- `400` = malformed request: validate the body and `application/json` content-type.
- `401` = token missing/expired: re-run `TokenRequest`.
- `403` = the client is not entitled for the channel/cobrand.
- No idempotency key is documented; do not blind-retry a `Redeem` on timeout —
  reconcile via the confirmation first. See `conventions/switchfly-conventions.yml`
  and `errors/switchfly-problem-types.yml`.
