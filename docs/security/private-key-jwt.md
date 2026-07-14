# private_key_jwt client authentication (RFC 7523)

Authenticate your client to the token / PAR / introspection / revocation endpoints with a
**signed assertion** instead of a shared `client_secret`. You register a public key (inline
JWKS or a `jwks_uri`); the client signs a short-lived, single-use JWT (`iss` = `sub` =
`client_id`, `aud` = the endpoint, fresh `jti`, short `exp`) with the matching private key.

No shared secret ever leaves your infrastructure, and key rotation is a JWKS update. The
accepted algorithms are advertised in
`token_endpoint_auth_signing_alg_values_supported` (RS/PS/ES 256/384/512). Both SDKs take a
PEM or JWK and handle assertion minting on every authenticated request.
