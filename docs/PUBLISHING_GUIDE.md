# Publishing Guide

Publishing a course to the OpenEdu Library is a four-step process. Metadata is
validated on PR, release assets are validated on publish, and the Pages site
deploys automatically — but the catalog is regenerated **manually** (see
[Step 4](#step-4--regenerate-the-catalog)).

```mermaid
flowchart LR
    A[Author: add courses/&lt;id&gt;/metadata.json] -->|PR| B[validate.yml]
    B -->|pass| C[Merge to main]
    C --> D[Author: build .oep with edu oep:build]
    D --> E[Author: gh release create &lt;id&gt;-v&lt;semver&gt;]
    E --> F[release-validate.yml]
    F -->|pass| G[Author: regenerate catalog.json]
    G -->|PR| H[catalog.json merged to main]
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

## Step 4 — Regenerate the catalog

After the release is validated (by `release-validate.yml`; see the
[Release Process](RELEASE_PROCESS.md)), regenerate `catalog.json` **locally** and
open a PR (the `main` branch requires changes via PR).

```bash
# requires a token; the repo is private
GITHUB_TOKEN=<token> npm run generate:catalog
# or: GITHUB_TOKEN=<token> npx --no-install open-edu-registry generate-catalog \
#       --repo <owner>/openedu-library --out catalog.json --strict
```

`npm run generate:catalog` uses `$GITHUB_REPOSITORY` (set in CI); locally, pass
the repo explicitly or set `GITHUB_REPOSITORY=<owner>/openedu-library`. Pre-release
releases are skipped by default — pass `--include-prerelease` to include them.

```bash
git checkout -b chore/catalog-regeneration
git add catalog.json
git commit -m "chore(catalog): regenerate catalog.json"
git push origin chore/catalog-regeneration
gh pr create --base main --head chore/catalog-regeneration \
  --title "chore(catalog): regenerate catalog.json"
```

Once merged, `deploy-pages.yml` publishes the updated catalog.

## Rules to remember

- **Never edit `catalog.json`** — it is generated.
- **Never commit `.oep` files** to the repo — they live in Releases.
- Patch a version by uploading a new release; **no metadata edits needed**.
- A release whose metadata is missing fails `release-validate.yml` — add metadata
  before publishing.
