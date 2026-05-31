# Rules for Editing RESTk YAML Files

## MUST Follow

1. **Never change the `id` field** — it's a Snowflake ID that links the file to the database entity.
   Changing it creates a duplicate instead of updating the existing entity.

2. **Never change the `version` field** — always keep it as `1`.

3. **Never edit `.restk-meta.json`** — it tracks workspace ownership for cross-workspace import safety.

4. **Keep IDs as quoted strings** — `id: "298356360100184083"` not `id: 298356360100184083`.
   Unquoted large integers lose precision in YAML/JSON parsers.

5. **Use valid YAML syntax** — the file must parse as valid YAML. Invalid files are skipped during import.

## SHOULD Follow

6. **Use `{{variable}}` references** instead of hardcoded values for URLs, tokens, and credentials.
   This keeps secrets out of the YAML files and enables environment switching.

7. **Put secrets in `.env`** — never hardcode API keys, tokens, or passwords in YAML files.
   The `.env` file is git-ignored and not synced.

8. **Keep the file naming convention** — `{METHOD}_{slug}.yaml` for requests (e.g., `POST_login.yaml`).
   The app generates these names from the method and request name.

9. **Don't rename directories** unless you also update the `name` field inside `collection.yaml` or `folder.yaml`.

10. **One request per file** — don't combine multiple requests into a single YAML file.

## Safe Operations

- ✅ Edit request URL, method, headers, body, scripts
- ✅ Edit collection/folder auth settings, scripts, variables
- ✅ Edit environment variables (except secret values)
- ✅ Add new request files (follow the naming convention)
- ✅ Delete request files (entity will be removed from the app)
- ✅ Move request files between folders (update `folderId` in the YAML)

## Unsafe Operations

- ❌ Change `id` fields (creates duplicates)
- ❌ Change `version` fields
- ❌ Edit `.restk-meta.json`
- ❌ Create files with invalid YAML syntax
- ❌ Use unquoted large integer IDs
