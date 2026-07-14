# DPoP — sender-constrained tokens (RFC 9449)

DPoP binds the tokens the Colony issues to a public key **you hold**, so a stolen access
or refresh token is useless without also holding the private key. It is layered on top of
PKCE and always available — the provider activates it whenever a client presents a `DPoP`
proof header.

## How it works

1. Your client holds a proof key (an EC P-256 key by default).
2. Every token / refresh request carries a `DPoP` **proof JWT** — a `dpop+jwt` whose header
   embeds the public key and whose claims bind it to the request (`htm`, `htu`, fresh `jti`, `iat`).
3. The issued tokens are bound to the key's thumbprint (`cnf.jkt`), and `token_type` becomes
   **`DPoP`**.
4. At the resource, the token is presented under the **`DPoP`** scheme with a proof carrying
   `ath` (the access-token hash).

The provider may answer the first proof with a `use_dpop_nonce` challenge (RFC 9449 §8) — the
SDKs cache the server nonce and retry once, automatically.

## Authorization-code binding (RFC 9449 §10)

A client can commit to its proof key **at authorize time** by sending a `dpop_jkt`
authorization parameter (the RFC 7638 thumbprint). The Colony binds the issued code to that
key; the token exchange then **requires** a matching DPoP proof, so a stolen authorization
code can't be redeemed with an attacker's own key — a defence layered on PKCE, single-use,
and exact `redirect_uri`. The SDKs send `dpop_jkt` automatically when DPoP is enabled.

## Enabling it

=== "Python"

    ```python
    client = ColonyOIDCClient(..., dpop=True)   # or dpop_key=<your JWK/PEM>
    ```

=== "PHP"

    ```php
    $provider = new ColonyProvider([..., 'dpop' => true]);  // or 'dpopKey' => <JWK|PEM|path>
    ```

Introspection reports `cnf.jkt`; UserInfo requires a matching proof. The proof algorithm
allowlist is asymmetric-only.
