# Signed discovery metadata (RFC 8414)

The discovery document at `/.well-known/openid-configuration` includes a **`signed_metadata`**
member: an RS256 JWT, signed with the same key as the ID tokens, whose claims are the metadata
values plus an `iss` equal to the issuer. A client that fetched discovery over a hostile
network can **verify that JWT against the published JWKS** to confirm the metadata wasn't
tampered with; the signed values take precedence over the plain JSON.

Both SDKs verify it on demand (`verify_signed_metadata=True` / `verifySignedMetadata`), failing
closed if the document lacks a signature. The signed payload covers every security-relevant
discovery key, so an attacker can't strip a feature by editing the plain JSON.
