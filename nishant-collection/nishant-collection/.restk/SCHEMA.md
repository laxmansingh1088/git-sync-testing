# RESTk YAML Schema Reference

This directory contains API collections, folders, requests, and environments
exported from the RESTk app. AI assistants and external tools can read and
modify these files — changes are automatically synced back to the app.

## Directory Structure

```
workspace-name/
├── .restk/                    # This docs directory
│   ├── SCHEMA.md              # Format reference (this file)
│   └── RULES.md               # Rules for editing
├── .restk-meta.json           # Workspace metadata (DO NOT EDIT)
├── .gitignore
├── .env                       # Secret values (git-ignored)
├── .env.sample                # Secret key names (committed)
├── environments/
│   └── {env-name}.yaml
├── {collection-slug}/
│   ├── collection.yaml
│   ├── {folder-slug}/
│   │   ├── folder.yaml
│   │   ├── {METHOD}_{slug}.yaml
│   │   └── ...
│   ├── runners/
│   │   └── {preset-name}.runner.yaml
│   └── {METHOD}_{slug}.yaml
└── ...
```

## Request YAML Format

```yaml
# Restk Request

id: "298356360100184083"     # Snowflake ID (quoted string, DO NOT CHANGE)
version: 1                    # Always 1
name: Login                   # Display name
method: POST                  # HTTP method: GET, POST, PUT, DELETE, PATCH, HEAD, OPTIONS
url: '{{base_url}}/api/login' # URL (supports {{variable}} syntax)
sortOrder: 1                  # Display order (float)
description: ""               # Optional description
folderId: "298356360091795464" # Parent folder ID (omitted if at collection root)
parameters:                   # Query parameters
    - id: "C1D99396-..."
      key: page
      value: "1"
      isEnabled: true
      order: 0
headers:                      # HTTP headers
    - id: "A2B33456-..."
      key: Content-Type
      value: application/json
      isEnabled: true
      isSecret: false
      order: 0
bodyType: raw                 # none, raw, form_data, x_www_form_urlencoded, binary, graphql
bodyContentType: json         # For raw: json, xml, html, text, javascript
bodyData:                     # Structured body (only relevant fields emitted)
    rawJSON: |
        {
          "email": "user@example.com",
          "password": "secret"
        }
authType: inherit             # none, inherit, bearer, basic, apikey, oauth2, aws, hawk, jwt, ntlm
isAuthOverridden: false
authConfigData: ""            # JSON string with auth credentials
prescript: ""                 # Pre-request script (JavaScript)
postscript: ""                # Post-request script (JavaScript)
skipParentScripts: false
variables:                    # Request-scoped variables
    - id: "uuid"
      key: var_name
      value: var_value
      order: 0
      enabled: true
```

## Collection YAML Format

```yaml
# Restk Collection

id: "298302917738233856"
version: 1
name: My API Collection
sortOrder: 1
isHidden: false
isSharedWithAI: true
authType: bearer
authConfigData: '{"bearerToken":"{{access_token}}"}'
prescript: ""
postscript: ""
variables:
    - id: "uuid"
      key: base_url
      value: https://api.example.com
      order: 0
      enabled: true
```

## Folder YAML Format

```yaml
# Restk Folder

id: "298356360091795464"
version: 1
name: Auth
sortOrder: 1
parentCollectionId: "298302917738233856"
parentFolderId: "298356360091795000"  # Omitted for root-level folders
authType: inherit
isAuthOverridden: false
authConfigData: ""
prescript: ""
postscript: ""
variables: []
```

## Environment YAML Format

```yaml
# Restk Environment

id: "298331136503840769"
version: 1
name: Development
variables:
    - key: host
      value: localhost:3000
      type: default              # default or secret
      enabled: true
    - key: api_key
      value: ""                  # Secret values stored in .env, not here
      type: secret
      enabled: true
```

## Runner Preset YAML Format

```yaml
# Restk Runner Preset

id: "500001"
version: 1
name: Smoke Tests
description: "Quick validation of critical endpoints"
collectionId: "298302917738233856"   # Parent collection
executionMode: sequential            # sequential or parallel
delayBetweenRequests: 0.5            # Seconds between requests (sequential only)
parallelLimit: 5                     # Max concurrent requests (parallel only)
isDefault: false
selectedRequestIds:                  # Which requests to run
    - "300001"
    - "300002"
    - "300003"
requestOrder:                        # Execution order (sequential only)
    - "300001"
    - "300002"
    - "300003"
sortOrder: 1
```

Runner preset files are stored in `{collection-slug}/runners/{preset-name}.runner.yaml`.

## Variable Syntax

- `{{variable_name}}` — references a variable from environment, collection, or folder scope
- Variables are resolved at runtime in order: request → folder → collection → environment
- Secret variables have their values in `.env` (git-ignored), not in the YAML files
