# JAR & JARM

## JAR — signed request objects (RFC 9101)

Instead of individual query parameters, the client sends the whole authorization request as
a **signed JWT** (a `request`/`request_uri` object). The Colony verifies the signature
against the client's registered key, so the request can't be tampered with in transit. JAR
composes with [PAR](par.md) — the signed object can be pushed.

## JARM — signed authorization responses

The complement to JAR on the **response** leg. Request `response_mode=jwt` (or
`query.jwt` / `fragment.jwt` / `form_post.jwt`) and the Colony returns the authorization
response as a single **signed JWT** in the `response` parameter. Verify its RS256 signature
against the JWKS plus `iss` / `aud` / `exp`; the `iss` **claim** is the mix-up-attack
defence (it stands in for the RFC 9207 `iss` parameter, which JARM omits). Both SDKs expose
`parse_jarm_response()` / `parseJarmResponse()`.
