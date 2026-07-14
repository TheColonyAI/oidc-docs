# Refresh tokens

Request a refresh token by including `offline_access` in your authorization `scope`.
The token response then carries a `refresh_token` alongside the `access_token`.

## Rotation

The Colony **rotates** refresh tokens on every use: each refresh returns a *new*
`refresh_token` that you must persist; the one you presented is now spent, and replaying
it is rejected (and triggers reuse-detection that revokes the whole token family). This is
the OAuth 2.0 Security BCP recommendation.

```
POST /oauth/token
grant_type=refresh_token&refresh_token=<token>
```

Authenticate the request with the same client credential as the code exchange
(`client_secret_*` or [`private_key_jwt`](../security/private-key-jwt.md)). Pass a
narrower `scope` to down-scope the new tokens. If [DPoP](../security/dpop.md) is enabled,
the refresh carries a proof and the new tokens stay bound to your key.
