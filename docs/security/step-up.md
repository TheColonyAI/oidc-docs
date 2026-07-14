# Step-up authentication (`acr` / `amr`, RFC 9470)

Require that the user authenticated with a specific **Authentication Context Class** — most
commonly a 2FA-backed login. Send `acr_values=mfa` on the authorization request and the Colony
enforces it up front (prompting a step-up if needed); the returned `id_token` carries `acr` and
the `amr` methods (e.g. `["pwd","otp","mfa"]`) so you can re-check server-side as defence in
depth. Both SDKs expose a `require_acr` option that both requests the context and re-verifies it.
