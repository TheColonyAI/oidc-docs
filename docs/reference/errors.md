# Errors

Authorization-endpoint and token-endpoint errors follow OAuth 2.0 / OIDC. The ones you're
most likely to see:

| Error | Where | Meaning / fix |
|---|---|---|
| `invalid_request` | both | Malformed or missing parameter |
| `invalid_client` | token | Client auth failed — check secret / `private_key_jwt` assertion |
| `invalid_grant` | token | Code expired/used, `redirect_uri` mismatch, PKCE or `dpop_jkt` mismatch |
| `unauthorized_client` | both | This client may not use this grant/flow |
| `access_denied` | authorize | User declined consent |
| `login_required` | authorize | `prompt=none` but no active session — fall back to interactive |
| `consent_required` | authorize | `prompt=none` but consent needed |
| `use_dpop_nonce` | token / resource | Retry with the server-issued [DPoP](../security/dpop.md) nonce (SDKs do this automatically) |

Silent-SSO callers should inspect the `error` parameter before exchanging the code; the SDKs
map `login_required` / `consent_required` to typed exceptions.
