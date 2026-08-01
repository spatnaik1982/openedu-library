# OpenEdu Course Registry

The **OpenEdu Library** is the official course registry for the OpenEdu runtime — a
GitHub-native, backend-free distribution channel. It stores **metadata only**; the
actual course packages (`.oep` files) live in [GitHub Releases](https://github.com/<owner>/openedu-library/releases).

## What lives here

| Path | Kind | Description |
| --- | --- | --- |
| `courses/<id>/metadata.json` | **authored** | Per-course display metadata (name, author, license, languages, tags) |
| `courses/<id>/README.md` | **authored** | Optional human-readable course page |
| `courses/<id>/thumbnail.png` | **authored** | Optional thumbnail (referenced from `metadata.json`) |
| `catalog.json` | **generated** | The machine-readable catalog consumed by the learner app. Never hand-edit. |
| `schemas/*.json` | **generated** | JSON Schema files regenerated from `@open-edu/schemas`. Never hand-edit. |
| `.github/workflows/*.yml` | **authored** | CI: validate metadata, validate release assets, deploy Pages (catalog is regenerated manually) |

## How the learner app finds courses

Point the OpenEdu learner app at the generated catalog:

```env
VITE_CATALOG_URL=https://<owner>.github.io/openedu-library/catalog.json
```

The catalog conforms to the framework's `CatalogSchema`, so existing install
(file, URL, catalog), update detection, and checksum verification work unchanged.

## All registry logic lives in the library

This repository contains **no logic**. Validation, catalog generation, release
checks, and JSON Schema generation all come from the published
[`@open-edu/registry`](https://www.npmjs.com/package/@open-edu/registry) package
(itself built on `@open-edu/schemas` and `@open-edu/oep-distribution`).
GitHub Actions invoke the `open-edu-registry` CLI from that package.

## Related docs

- [Catalog Spec](CATALOG_SPEC.md) — the `catalog.json` contract
- [Metadata Spec](METADATA_SPEC.md) — the `metadata.json` contract
- [Publishing Guide](PUBLISHING_GUIDE.md) — how to add a course
- [Release Process](RELEASE_PROCESS.md) — how a course release is validated
- [Architecture](ARCHITECTURE.md) — end-to-end flow and tooling
