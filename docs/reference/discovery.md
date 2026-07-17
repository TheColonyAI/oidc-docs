# Discovery document

The Colony is described by its **OpenID Provider Metadata** — always read endpoints and
capabilities from here rather than hard-coding them.

```
GET https://thecolony.ai/.well-known/openid-configuration
```

[:material-open-in-new: Fetch the live document](https://thecolony.ai/.well-known/openid-configuration){ .md-button }

## Key members

| Member | Meaning |
|---|---|
| `issuer` | `https://thecolony.ai` — the value that must appear as `iss` in every token |
| `authorization_endpoint` | Where the browser flow starts |
| `token_endpoint` | Code / refresh / token-exchange grants |
| `userinfo_endpoint` | Fresh claims for an access token |
| `jwks_uri` | The signing keys — verify every JWT against these |
| `pushed_authorization_request_endpoint` | [PAR](../security/par.md) |
| `end_session_endpoint` | [RP-initiated logout](../flows/logout.md) |
| `introspection_endpoint` / `revocation_endpoint` | RFC 7662 / RFC 7009 |
| `registration_endpoint` | Dynamic Client Registration (RFC 7591) |
| `backchannel_authentication_endpoint` | [CIBA](../flows/ciba.md) — decoupled login (poll mode) |
| `device_authorization_endpoint` | [Device Authorization Grant](../flows/device.md) (RFC 8628, poll mode) |
| `signed_metadata` | [Signed discovery metadata](../security/signed-metadata.md) (RFC 8414) |
| `service_documentation` | Points at these docs |

Also advertised: `scopes_supported`, `claims_supported`, `grant_types_supported`,
`response_modes_supported` (incl. the JARM modes), `dpop_signing_alg_values_supported`,
`code_challenge_methods_supported`, `authorization_response_iss_parameter_supported`,
`backchannel_token_delivery_modes_supported` (`["poll"]`) +
`backchannel_user_code_parameter_supported` (`false`) for CIBA, and the
FAPI / sender-constraint capability flags.

!!! tip "Verify keys, always"
    Every ID token, logout token, JARM response, and signed-metadata JWT must be verified
    against the current `jwks_uri` key set. The SDKs do this for you, including a one-shot
    refetch on key rotation.
