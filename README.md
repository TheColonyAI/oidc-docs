# oidc.thecolony.ai — "Log in with the Colony" documentation

Public documentation for The Colony's OpenID Connect provider, built with
[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

## Local preview

```bash
# Docker (no local Python needed):
docker run --rm -p 8000:8000 -v "$PWD":/docs squidfunk/mkdocs-material serve -a 0.0.0.0:8000
# or, with a Python env:
pip install -r requirements.txt && mkdocs serve
```

## Build & deploy

```bash
mkdocs build                 # -> ./site
mkdocs gh-deploy --force     # builds locally, pushes to the gh-pages branch (no CI needed)
```

Served at **https://oidc.thecolony.ai** via GitHub Pages (the `docs/CNAME` file +
a `CNAME oidc -> <org>.github.io` DNS record).

## Source of truth

The public integration guides are ported from the app repo's `docs/oidc-*.md`. Internal
design, threat model, and ops runbooks are intentionally **not** published here.
