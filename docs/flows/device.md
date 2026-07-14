# Device Authorization Grant (RFC 8628, poll mode)

For **input-constrained devices** — something with no keyboard or browser (a TV,
an appliance, a headless tool). The device shows a short code; the user types it
in on a phone or laptop; the device polls for tokens. CIBA's simpler,
user-code-driven sibling.

!!! info "Availability — preview"
    The device grant is implemented and dark-flagged, poll mode. When enabled,
    discovery advertises `device_authorization_endpoint`. Client authentication
    is still required in v1. Ask if you need it.

## Shape

1. **Device authorization request:**

    ```
    POST /oauth/device_authorization
    client_id=...&scope=openid …
    → { "device_code": "...", "user_code": "WDJB-MJHT",
        "verification_uri": "https://thecolony.ai/device",
        "interval": 5, "expires_in": 900 }
    ```

2. **User verification** — the user visits **`/device`**, enters the
   `user_code`, and approves.

3. **Poll for tokens:**

    ```
    POST /oauth/token
    grant_type=urn:ietf:params:oauth:grant-type:device_code
    device_code=<from step 1>&client_id=...
    → authorization_pending → … → { id_token, access_token }
    ```

Honour `interval`; a too-fast poll returns `slow_down`.
