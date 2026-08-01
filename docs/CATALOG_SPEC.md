# Catalog Spec

`catalog.json` is the machine-readable catalog that the OpenEdu learner app
fetches at runtime. It is **generated** by the `open-edu-registry generate-catalog`
command and must **never be hand-edited** — the `generate-catalog` GitHub Actions
workflow regenerates it from `courses/*/metadata.json` + GitHub Releases on every
release, and `validate:catalog` checks it in CI.

## Shape

```jsonc
{
  "catalogVersion": 1,
  "generatedAt": "2026-08-01T00:00:00.000Z",
  "packages": [
    {
      "id": "tribal-art",
      "title": "Indian Tribal Art",
      "description": "Explore the traditional art forms of India.",
      "author": "OpenEdu Authors",
      "license": "CC-BY-SA-4.0",
      "tags": ["art", "india"],
      "thumbnail": "https://raw.githubusercontent.com/<owner>/openedu-library/HEAD/courses/tribal-art/thumbnail.png",
      "latestVersion": "0.4.0",
      "versions": [
        {
          "version": "0.4.0",
          "downloadUrl": "https://github.com/<owner>/openedu-library/releases/download/tribal-art-v0.4.0/tribal-art-0.4.0.oep",
          "checksum": "<64-char-sha256>",
          "sizeBytes": 20480,
          "languages": ["en"]
        }
      ]
    }
  ]
}
```

## Field semantics

| Field | Notes |
| --- | --- |
| `catalogVersion` | Literal `1`. |
| `generatedAt` | ISO timestamp when the catalog was generated. |
| `packages[].id` | Course id (must match the release tag prefix and metadata `id`). |
| `packages[].title` | Display title (from metadata `name`). |
| `packages[].description` / `author` / `license` / `tags` / `thumbnail` | Display metadata, copied from `metadata.json`. |
| `packages[].latestVersion` | Highest semver across `versions`. |
| `packages[].versions[]` | **Sorted ascending** by semver — the learner app treats the last element as the latest. |
| `versions[].downloadUrl` | Absolute GitHub release-download URL (generated, never stored). |
| `versions[].checksum` | SHA-256 of the `.oep` asset, **computed** by the generator (never trusted from the publisher). |
| `versions[].sizeBytes` | Release asset size from the GitHub API. |
| `versions[].languages` | From metadata `languages`. |

## Validation

Run locally:

```bash
npm run validate:catalog
```

`validate.yml` runs this on every push/PR. The catalog is regenerated only when a
release is published (`generate-catalog.yml`).

## Schema

The authoritative Zod schema is `CatalogSchema` in `@open-edu/schemas`. A draft-07
JSON Schema export is committed at `schemas/catalog.schema.json` (regenerated with
`npm run generate:schemas`).
