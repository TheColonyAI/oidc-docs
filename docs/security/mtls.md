# mTLS — certificate-bound tokens (RFC 8705)

Mutual-TLS gives you two things: **`tls_client_auth` / `self_signed_tls_client_auth`** client
authentication, and **certificate-bound access tokens** — the token is bound to the client
certificate's `x5t#S256` thumbprint (recorded as `cnf.x5t#S256`), so it can only be used from
a connection presenting that certificate.

!!! info "Deployment prerequisite"
    mTLS needs the edge (nginx) to be configured to request and forward the client
    certificate. Discovery advertises `tls_client_certificate_bound_access_tokens` and the
    supported client-auth methods when it's enabled.
