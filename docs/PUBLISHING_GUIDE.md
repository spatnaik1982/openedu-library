# Publishing Guide

Publishing a course to the OpenEdu Library is a three-step process. Everything is
automated after that: metadata is validated on PR, release assets are validated on
publish, and the catalog + Pages site regenerate automatically.

```mermaid
flowchart LR
    A[Author: add courses/&lt;id&gt;/metadata.json] -->|PR| B[validate.yml]
    B -->|pass| C[Merge to main]
    C --> D[Author: build .oep with edu oep:build]
    D --> E[Author: gh release create &lt;id&gt;-v&lt;semver&gt;]
    E --> F[release-validate.yml]
    F -->|pass| G[generate-catalog.yml]
    G --> H[catalog.json updated on main]
    H --> I[deploy-pages.yml]
    I --> J[GitHub Pages catalog.json]
    J --> K[Learner app fetches via VITE_CATALOG_URL]
```

## Step 1 — Add metadata (one-time)

Create `courses/<id>/metadata.json` and open a PR. `validate.yml` checks it
against `RegistryMetadataSchema`.

```bash
mkdir courses/my-course
# edit courses/my-course/metadata.json
npm run validate:metadata
```

See the [Metadata Spec](METADATA_SPEC.md) for the field reference.

## Step 2 — Build the package

Build the `.oep` with the OpenEdu CLI (in the `open-edu` monorepo). The output
filename already follows the required `<id>-<version>.oep` convention:

```bash
pnpm --filter @open-edu/cli build
node packages/cli/dist/cli.js oep:build ./my-course -o ./dist
```

## Step 3 — Create a release

Create a GitHub release with the naming convention:

- Tag: `<id>-v<major>.<minor>.<patch>` (e.g. `my-course-v1.2.0`)
- Assets:
  - `<id>-<version>.oep` (required)
  - `checksums.txt` (required) — lines of `<sha256>  <filename>`

```bash
gh release create my-course-v1.2.0 \
  dist/my-course-1.2.0.oep \
  checksums.txt \
  --title "my-course v1.2.0"
```

On publish, `release-validate.yml` verifies the assets (see the
[Release Process](RELEASE_PROCESS.md)), then `generate-catalog.yml` regenerates
`catalog.json` and `deploy-pages.yml` publishes it.

## Rules to remember

- **Never edit `catalog.json`** — it is generated.
- **Never commit `.oep` files** to the repo — they live in Releases.
- Patch a version by uploading a new release; **no metadata edits needed**.
- A release whose metadata is missing fails `release-validate.yml` — add metadata
  before publishing.
