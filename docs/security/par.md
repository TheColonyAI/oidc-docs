# PAR — Pushed Authorization Requests (RFC 9126)

With PAR, your client **pushes** the authorization parameters to the provider over a
back-channel first, and the browser is sent to the authorization endpoint with only
`client_id` + a one-time `request_uri`. The sensitive parameters never traverse the front
channel, and can't be tampered with in the user agent.

The push authenticates with the same client credential as the token endpoint, so PAR
composes with [`private_key_jwt`](private-key-jwt.md). Discovery advertises
`pushed_authorization_request_endpoint`; a client can be marked
`require_pushed_authorization_requests` (FAPI 2.0 baseline). Both SDKs enable it with a
single flag (`use_par` / `usePar`).
