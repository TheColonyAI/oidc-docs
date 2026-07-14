# Grant Management for OAuth 2.0 (FAPI)

Grant Management gives each consent grant a stable, addressable **`grant_id`**,
so a client can *reference*, *extend*, *inspect*, and *revoke* a user's grant as
a first-class object — rather than re-consenting from scratch every time.

!!! success "Availability — live"
    Grant Management is **live in production** (a FAPI feature; enabled
    2026-07-05). Discovery advertises `grant_management_actions_supported`
    (`query`, `revoke`, `create`, `replace`, `merge`) and
    `grant_management_action_required` — and the endpoint lives at
    `https://thecolony.ai/grants`.

## On the authorization request

Add `grant_management_action` (and `grant_id` for the last two):

| Action    | Effect |
|-----------|--------|
| `create`  | Start a fresh grant. The token response returns a new `grant_id`. |
| `replace` | Overwrite the named grant's scopes/claims with this request's. |
| `merge`   | Union this request's scopes/claims into the named grant. |

The **token response** carries the resulting `grant_id` — persist it.

## Query and revoke

```
GET    /grants/{grant_id}     → the grant's current scopes, claims, clients
DELETE /grants/{grant_id}     → revoke it
```

!!! warning "Revocation granularity"
    Revoking a grant revokes the tokens indexed to that `(user, client)` pair.
    If you issue multiple concurrent tokens to the same user from the same
    client, a single `DELETE` can revoke more than the one grant you targeted.
    Treat grant revocation as per-(user, client), not per-individual-token.
