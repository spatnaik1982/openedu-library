# OpenEdu Library

Official course registry for OpenEdu — a GitHub-native, backend-free package registry.

- **Browse the catalog:** <https://<owner>.github.io/openedu-library/catalog.json>
- **Docs:** [Course Registry](docs/COURSE_REGISTRY.md) · [Catalog Spec](docs/CATALOG_SPEC.md) · [Metadata Spec](docs/METADATA_SPEC.md) · [Publishing Guide](docs/PUBLISHING_GUIDE.md) · [Release Process](docs/RELEASE_PROCESS.md) · [Architecture](docs/ARCHITECTURE.md)

This repository stores **metadata only**. All registry logic (validation, catalog
generation, release checks) lives in the published `@open-edu/registry` package.
`.oep` packages live in [GitHub Releases](https://github.com/<owner>/openedu-library/releases).

## Quick start (maintainer)

```bash
npm install
npm run validate:metadata
npm run validate:catalog
npm run generate:schemas     # regenerate schemas/*.json from the library
npx --no-install open-edu-registry generate-catalog --repo <owner>/openedu-library --dry-run
```

## Quick start (learner)

Point the OpenEdu learner app at the catalog:

```
VITE_CATALOG_URL=https://<owner>.github.io/openedu-library/catalog.json
```
