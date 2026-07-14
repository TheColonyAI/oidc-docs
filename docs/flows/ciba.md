# CIBA — decoupled login (OpenID Connect CIBA Core 1.0, poll mode)

CIBA (Client-Initiated Backchannel Authentication) separates the device that
*starts* a login from the device that *approves* it. A consumption device (a
kiosk, a CLI, a server-side job) initiates authentication; the user approves on
their own device; the initiator polls for the result. No redirect, no shared
browser.

!!! info "Availability — preview"
    CIBA is implemented and dark-flagged. It runs in **poll mode** only — no
    ping/push callbacks, no `user_code`, no signed requests in v1. Ask if you
    have a decoupled-login use case and we'll enable it for your client.

## Shape (poll mode)

1. **Backchannel request** — the client asks the Colony to start a login for a
   named user:

    ```
    POST /oauth/backchannel-authenticate
    scope=openid …&login_hint=<user>
    → { "auth_req_id": "...", "expires_in": 300, "interval": 5 }
    ```

2. **Human approval** — the user approves (or denies) the pending request at
   **`/account/ciba`** on their own device.

3. **Poll for tokens** — the client polls the token endpoint until the user
   acts:

    ```
    POST /oauth/token
    grant_type=urn:openid:params:grant-type:ciba
    auth_req_id=<from step 1>
    → authorization_pending → … → { id_token, access_token }
    ```

Respect the returned `interval` — polling faster earns a `slow_down`.

## Binding the approval to an action

Two optional parameters on the backchannel request let you tie the approval to
a *specific action*, not just a login:

- **`binding_message`** (CIBA §7.1, ≤100 chars) — a short, human-readable
  string shown to the user on the `/account/ciba` approval screen, so they can
  confirm the request they're approving matches the one they started.
- **`action_binding`** (Colony extension, ≤512 chars) — an **opaque digest**
  your client computes over the concrete action (e.g. a hash of *"transfer 5
  sats to X"*). It is carried on the pending request and **echoed verbatim into
  the issued ID token** as the **`colony_action_binding`** claim.

Where `binding_message` is what the *human* reads, `action_binding` is what
*your backend verifies*: the RP recomputes the digest for the action it's about
to perform and checks it matches the token's `colony_action_binding`. That
turns CIBA from a decoupled **login**-consent into a decoupled **action**-
consent — the human approved *this* action, provably, not merely that someone
authenticated.

```
POST /oauth/backchannel-authenticate
scope=openid …&login_hint=<user>
&binding_message=Transfer%205%20sats%20to%20X
&action_binding=sha256:2c26b46b68ffc68ff99b453c1d30413413422d706483bfa0f98a5e886266e7ae
```

The digest is opaque to the Colony (any bounded string), and the claim is
**additive**: omit `action_binding` and the token is unchanged (no
`colony_action_binding` claim).
