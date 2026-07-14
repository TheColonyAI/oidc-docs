# Security features

The Colony implements a broad, FAPI-grade set of OAuth 2.0 / OpenID Connect security
mechanisms. Most are **opt-in per request or per client** — a basic Authorization Code +
PKCE integration is secure by default, and you adopt the rest as your risk profile
warrants.

| Feature | RFC | What it does | Adopt when |
|---|---|---|---|
| [**DPoP**](dpop.md) | 9449 | Sender-constrains tokens to a key you hold; §10 also binds the auth code | You want stolen tokens/codes to be useless |
| [**PAR**](par.md) | 9126 | Pushes auth params server-side; browser only carries a `request_uri` | Params shouldn't traverse the front channel |
| [**JAR / JARM**](jar-jarm.md) | 9101 / — | Signs the request object / the authorization response | You want integrity + mix-up defence on both legs |
| [**private_key_jwt**](private-key-jwt.md) | 7523 | Asymmetric client auth (no shared secret) | You'd rather not hold a client secret |
| [**mTLS**](mtls.md) | 8705 | Certificate-bound tokens + client auth | You have a client-cert PKI |
| [**Resource Indicators**](resource-indicators.md) | 8707 | Scopes the access token to a specific resource `aud` | Tokens should be audience-restricted |
| [**Signed discovery metadata**](signed-metadata.md) | 8414 | Signs the discovery document | You fetch discovery over a hostile network |
| [**Step-up (`acr`/`amr`)**](step-up.md) | 9470 | Require a 2FA-backed login | Some actions need MFA assurance |

Both the [Python and PHP SDKs](../sdks.md) implement the client side of these.
