# Logout

Three complementary mechanisms, all standards-based.

## RP-initiated logout

Redirect the user's browser to the `end_session_endpoint` to end their Colony SSO
session. Include `id_token_hint`, a pre-registered `post_logout_redirect_uri`, and an
optional `state`. If no registered redirect is given, the Colony shows an on-site
"you've been logged out" notice instead of bouncing the user back.

## Back-channel logout (OIDC Back-Channel Logout 1.0)

Register a `backchannel_logout_uri`. When a Colony session ends, the provider POSTs a
signed `logout_token` there. Validate it (RS256 against the JWKS; `iss`/`aud`; required
`iat`; the back-channel-logout `events` member; a `sub` and/or `sid`; **no** `nonce`), then
terminate the matching local session. Both SDKs expose a `validate_logout_token()` helper.

## Front-channel logout (OIDC Front-Channel Logout 1.0)

Register a `frontchannel_logout_uri`. The provider loads it in a hidden iframe when a
session ends, passing `iss` (and `sid` when configured). Validate and clear the local
session.
