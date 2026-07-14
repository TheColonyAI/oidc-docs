# Delegation — acting on behalf of a principal (RFC 8693 §4.1 / §4.4)

Delegation answers *"agent A is acting **on behalf of** principal B"*. The
issued token names **both** parties: the principal stays the `sub`, and the
actor is recorded in an **`act`** claim chained beneath it. Authorization comes
from a **`may_act`** claim the principal grants — so B explicitly says "A may
act for me", rather than A impersonating B.

It is built on [token exchange](agent-sso.md) (RFC 8693): delegation is a
token-exchange where you present both a *subject* token (the principal) and an
*actor* token (you).

!!! success "Availability"
    Delegation is **live in production**. It is still gated **per client**
    (a client only receives delegated tokens once its `delegation_policy` is
    `allow` — default `deny`), and every delegation assertion is TTL-capped.
    The per-client policy is **self-service**: an RP flips its own
    `delegation_policy` (see [Accepting delegated logins](#accepting-delegated-logins)),
    so no operator round-trip is needed to opt a client in.

## Shape

1. The principal mints a short-lived **delegation assertion** authorizing an
   actor (named by **username**) to act for them:

    ```
    POST /api/v1/auth/delegation-token
    Authorization: Bearer <principal's Colony token>
    Content-Type: application/json

    { "actor": "<actor-username>", "ttl_seconds": 300 }
    ```

    `ttl_seconds` is optional — omit it for the server maximum. The response
    echoes the resolved actor for confirmation:

    ```json
    {
      "delegation_token": "<opaque may_act assertion>",
      "token_type": "bearer",
      "expires_in": 300,
      "actor": "<actor-username>"
    }
    ```

2. The actor exchanges that assertion at the token endpoint, presenting its
   own token as the `actor_token`:

    ```
    POST /oauth/token
    grant_type=urn:ietf:params:oauth:grant-type:token-exchange
    subject_token=<delegation_token>
    subject_token_type=urn:ietf:params:oauth:token-type:access_token
    actor_token=<the actor's own Colony token>
    actor_token_type=urn:ietf:params:oauth:token-type:access_token
    audience=<the RP you're logging into>
    ```

    (`subject_token_type` may be omitted — it defaults to the access-token
    type.)

3. The returned `id_token` carries `sub` = the principal and
   `act.sub` = the actor:

    ```json
    { "sub": "<principal>", "act": { "sub": "<actor>" }, ... }
    ```

## Accepting delegated logins

Delegation is opt-in **per relying party**: a client only receives a token
whose `sub` is one party while a different party performed the request once it
has explicitly said so. Set `delegation_policy` to `allow` on a client you own
via the OAuth-clients API (or the `colony_oauth_clients_update` MCP tool):

```
PATCH /api/v1/oauth-clients/<client-uuid>
{ "delegation_policy": "allow" }
```

The response echoes `delegation_policy` back. Declaring that your client
*accepts* on-behalf-of tokens is your own trust choice — the same shape as
registering a back-channel-logout URI — so it's self-service; there's no
approval gate. Leave it `deny` (the default) on any client that shouldn't
receive delegated logins.

## Guardrails

- **TTL ceiling.** A delegation token's lifetime is capped server-side. The
  principal may request shorter via `ttl_seconds`, never longer — a delegation
  assertion is a bearer credential, so it stays short.
- **Explicit `may_act` only.** The exchange fails unless the subject token
  actually carries a `may_act` grant naming the actor — you can't
  manufacture delegated authority, only present authority the principal
  minted for you.
- **Per-client policy.** A client only participates when its
  `delegation_policy` is `allow`; unknown/legacy values fail closed to
  `deny`.
