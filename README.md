# Managed signature templates

The desktop app downloads official signature templates from the `manifest.json`
in this folder over plain HTTPS. **No GitHub/GitLab API or token is used** — just a
GET on a raw file URL — so the source is fully portable between providers.

## How the app finds the manifest

1. If any `config.json` (ProgramData → APPDATA → bundled) sets
   `"templatesManifestUrl"`, that URL is used.
2. Otherwise the built-in default is used:
   `https://raw.githubusercontent.com/aliviado26/geidi-signature-templates/main/manifest.json`

The app repo is **private**, but `raw.githubusercontent.com` only serves files from
**public** repos without a token. So the templates live in a **separate public repo**
(`geidi-signature-templates`), with these files at the **repo root** (not in a
`templates/` subfolder). Push the contents of this folder to that public repo's `main`
branch and the default URL resolves with no config change. This folder stays in the app
repo as the editable working copy.

## Switching host (GitHub ↔ GitLab)

Only the manifest URL changes. Set `templatesManifestUrl` in `config.json`:

- **GitHub raw:** `https://raw.githubusercontent.com/<user>/<repo>/<branch>/templates/manifest.json`
- **GitLab raw:** `https://gitlab.com/<user>/<repo>/-/raw/<branch>/templates/manifest.json`

Template `url` values in the manifest are **relative** (e.g. `standard.html`), so they
resolve against wherever the manifest lives — nothing else needs editing to move hosts.

## manifest.json format

```json
{
  "version": "1.0.0",
  "templates": [
    { "id": "geidi-standard", "name": "Geidi Corporate Signature",
      "version": "1.0.0", "type": "standard", "url": "standard.html" }
  ]
}
```

- Bump the top-level `version` whenever you publish changes — the app compares it
  against the locally cached version to flag "update available".
- `url` may be relative (resolved against the manifest folder) or an absolute URL.

## Template HTML

Templates use the same placeholders as the in-app builder:
`{{FirstName}} {{LastName}} {{DisplayName}} {{JobTitle}} {{Department}} {{Email}} {{Phone}}`,
and `__GEIDI_LOGO_SRC__` for the app's bundled logo. Managed templates are
**read-only** in the app; users pick "Duplicate to customize" to make an editable copy.
