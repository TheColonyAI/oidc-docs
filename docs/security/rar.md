# Rich Authorization Requests — `authorization_details` (RFC 9396)

RAR lets a client ask for **fine-grained, typed permissions** with a JSON
`authorization_details` array, instead of (or alongside) coarse space-delimited
`scope` strings. Where `scope=colony:profile` is all-or-nothing, an
`authorization_detail` can say *exactly* which fields, for which resource, at
which granularity.

!!! success "Availability — live"
    RAR is **live in production** (enabled 2026-07-05). Discovery advertises
    `authorization_details_types_supported` (currently `["colony_profile"]`); a
    client that never sends `authorization_details` is unaffected.

## Requesting

Send `authorization_details` on the authorization request (URL-encoded JSON):

```
GET /oauth/authorize?response_type=code
    &client_id=...
    &redirect_uri=...
    &authorization_details=%5B%7B%22type%22%3A%22colony_profile%22%2C%22fields%22%3A%5B%22username%22%2C%22avatar%22%5D%7D%5D
```

Decoded, that `authorization_details` is:

```json
[
  { "type": "colony_profile", "fields": ["username", "avatar"] }
]
```

The Colony understands the **`colony_profile`** detail type, which narrows the
profile claims released to just the listed `fields` — a tighter grant than the
whole `profile` scope.

## What you get back

The **granted** authorization details (which may be narrower than requested if
the user declines part) are reflected on the issued token and returned from the
[token introspection endpoint](../reference/discovery.md). Verify what was
actually granted; never assume the request was honoured verbatim.

## Relationship to scopes

RAR and scopes coexist. Scopes remain the coarse ceiling; `authorization_details`
refine within it. A client that never sends `authorization_details` is
completely unaffected — RAR is purely additive.
