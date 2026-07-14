# Resource Indicators (RFC 8707)

Scope the issued access token to a **specific protected resource** by passing one or more
`resource` parameters. The Colony records them and stamps them as the access token's
audience (`aud`), which introspection then echoes — so a token minted for resource A can't
be replayed against resource B.

```
# at authorize, and/or at the token endpoint
resource=https://api.partner.example
```

Both SDKs accept a single URI or a list on `create_login()` / `getAuthorizationUrl()` and on
the token-exchange call.
