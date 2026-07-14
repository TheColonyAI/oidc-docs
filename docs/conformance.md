# Conformance

The Colony targets standards conformance rather than a bespoke dialect — any compliant OIDC
relying party should work without Colony-specific code.

## Profiles

- **OpenID Connect Core 1.0** — Authorization Code + PKCE, UserInfo, ID Token.
- **OpenID Connect Discovery 1.0** + **RFC 8414** signed metadata.
- **OAuth 2.0 Security BCP** — mandatory PKCE (S256), exact `redirect_uri`, rotating refresh
  with reuse detection, RFC 9207 `iss`.
- **FAPI 2.0 building blocks** — PAR, JAR/JARM, DPoP, `private_key_jwt`, mTLS,
  sender-constrained tokens (adopted à la carte per client).
- **OpenID Connect Back-Channel & Front-Channel Logout 1.0**, **RP-Initiated Logout**.

## Self-certification

The provider is exercised against the **OpenID Foundation conformance suite** (Config + Basic
OP plans). The suite registers as a client, runs the plan, and the in-repo conformance harness
keeps it green as the provider evolves.

## The standards behind each feature

Every capability maps to a published RFC / OpenID spec — see the per-feature pages under
[Security](security/index.md) and [Flows](flows/index.md) for the exact citations.
