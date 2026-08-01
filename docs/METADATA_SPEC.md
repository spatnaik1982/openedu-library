# Metadata Spec

`courses/<id>/metadata.json` is the **only file an author edits**. It holds
display metadata for a course. The authoritative Zod schema is
`RegistryMetadataSchema` in `@open-edu/schemas`; a draft-07 JSON Schema export is
committed at `schemas/metadata.schema.json` (regenerated with
`npm run generate:schemas`).

## Fields

| Field | Required | Type | Notes |
| --- | --- | --- | --- |
| `id` | ✅ | string | Kebab-case: `^[a-z0-9][a-z0-9_-]*$`. Must match the release tag prefix and the directory name. |
| `name` | ✅ | string | Display title (surfaces as `title` in the catalog). |
| `author` | ✅ | string | Author/org name. |
| `license` | ✅ | string | e.g. `CC-BY-SA-4.0`, `MIT`. |
| `languages` | ✅ | string[] | BCP-47 tags, e.g. `["en"]`. At least one. |
| `description` | — | string | Short course description. |
| `version` | — | string | **Informational only.** Catalog versions always come from GitHub Releases; a patch release needs zero metadata edits. |
| `thumbnail` | — | string | Relative path to an image in the course dir, e.g. `thumbnail.png`. Must match `^[A-Za-z0-9_./-]+\.(webp|png|jpg|jpeg|avif)$`. |
| `screenshots` | — | string[] | Relative paths to screenshots in `screenshots/`. |
| `tags` | — | string[] | Free-form tags. |
| `type` | — | `"course"` \| `"bundle"` | Defaults to `"course"`. |

Unknown fields are rejected (strict schema).

## Directory ↔ id rule

The directory name and the `id` field must agree:

```
courses/tribal-art/
└── metadata.json        # { "id": "tribal-art", ... }
```

Release tags use the same id: `tribal-art-v0.4.0`.

## Validation

Run locally:

```bash
npm run validate:metadata
```

`validate.yml` runs this on every push/PR and fails on missing files, invalid
JSON, schema violations, or duplicate ids.
