# ATC Verifier

**Reference SDK for verifying [Agent Trust Credential (ATC) v2.0](https://github.com/eddyflores100-lang/atc-spec)** — open standard for trust credentials in agent commerce.

**Status:** Bootstrapping. Implementation begins in Sprint 1 of the [90-day execution plan](https://github.com/eddyflores100-lang/atc-spec/blob/main/drafts/EXECUTION_PLAN_90days.md).

---

## What this will be

Two SDKs in this repo (or split into `atc-verifier-python` and `atc-verifier-ts` if it grows):

### Python SDK: `marketnow-atc-verifier`

```bash
pip install marketnow-atc-verifier
```

```python
from marketnow_atc_verifier import verify_atc, issue_atc, ATCBuilder

# Verify an ATC offline (no network call)
result = verify_atc("path/to/atc.json", offline=True)
if result["valid"]:
    print(f"Trust level: {result['trust_claims']}")
else:
    print(f"Invalid: {result['reason']}")

# Issue an ATC (requires Ed25519 private key)
atc = (ATCBuilder()
    .subject_type("skill")
    .credential_subject({"skill_id": "my-skill", "upstream_repo": "..."})
    .issuer_did("did:key:z6Mk...")
    .add_claim("filesystem_write", False)
    .add_claim("owasp_top_10.mcp01_tool_poisoning", "passed")
    .expiration_days(90)
    .build())

signed_atc = issue_atc(atc, private_key_path="issuer.key")
```

### TypeScript SDK: `@alicelabs/atc-verifier`

```bash
npm install @alicelabs/atc-verifier
```

```typescript
import { verifyATC, issueATC, ATCBuilder } from '@alicelabs/atc-verifier';

const result = await verifyATC('path/to/atc.json', { offline: true });
if (result.valid) {
  console.log(`Trust level: ${result.trust_claims}`);
}

const atc = new ATCBuilder()
  .subjectType('skill')
  .credentialSubject({ skill_id: 'my-skill', upstream_repo: '...' })
  .issuerDid('did:key:z6Mk...')
  .addClaim('filesystem_write', false)
  .expirationDays(90)
  .build();

const signedATC = await issueATC(atc, { privateKeyPath: 'issuer.key' });
```

### CLI

```bash
# Verify
npx @alicelabs/atc-verifier verify path/to/atc.json --offline

# Issue
npx @alicelabs/atc-verifier issue input.json --key issuer.key --out atc.json

# List trusted issuers
npx @alicelabs/atc-verifier registry --list
```

---

## Roadmap (from the 90-day plan)

| Sprint | Days | Target |
|---|---|---|
| 1 | 1-15 | Python package structure + did_key + jcs primitives |
| 2 | 16-30 | `verify_atc_offline()` + `issue_atc()` + PyPI v0.1.0 |
| 3 | 31-45 | TypeScript port + npm v0.1.0 + A2A extension |
| 5 | 61-75 | v0.2.0 with bug fixes from adoption pilots |
| 6 | 76-90 | v1.0.0 stable (semver committed) |

---

## Architecture (planned)

```
atc-verifier/
├── python/                          # marketnow-atc-verifier
│   ├── marketnow_atc_verifier/
│   │   ├── __init__.py
│   │   ├── did_key.py              # did:key → Ed25519 public key
│   │   ├── jcs.py                  # RFC 8785 canonicalization
│   │   ├── verify.py               # verify_atc() + verify_atc_offline()
│   │   ├── issue.py                # issue_atc()
│   │   ├── ocsp.py                 # OCSP with stapling
│   │   ├── cli.py                  # CLI entrypoint
│   │   └── schema.json             # ATC v2.0 JSON Schema (copy from atc-spec)
│   ├── tests/
│   ├── pyproject.toml
│   └── README.md
│
├── typescript/                     # @alicelabs/atc-verifier
│   ├── src/
│   │   ├── index.ts
│   │   ├── did-key.ts
│   │   ├── jcs.ts
│   │   ├── verify.ts
│   │   ├── issue.ts
│   │   ├── ocsp.ts
│   │   └── cli.ts
│   ├── test/
│   ├── package.json
│   ├── tsconfig.json
│   └── README.md
│
├── test-vectors/                   # Cross-impl test vectors
│   ├── valid/
│   └── invalid/
│
└── README.md  (this file)
```

---

## Dependencies (planned, minimal)

### Python
- `pynacl` — Ed25519 implementation
- `base58` — did:key decoding
- `requests` — OCSP (optional, only if online)
- `jsonschema` — ATC schema validation

### TypeScript
- `@noble/ed25519` — Ed25519
- `base-58` — did:key decoding
- `ajv` — JSON Schema validation
- `fetch` (built-in) — OCSP

Zero heavy frameworks. Zero vendor lock-in. MIT.

---

## License

AliceLabs Source-Available License v1.0 (AL-1.0) — see [LICENSE-AL-1.0](./LICENSE-AL-1.0). Commercial use requires a separate commercial license. Contact: legal@alicelabs.site

---

## Contributing

See [CONTRIBUTING.md in atc-spec](https://github.com/eddyflores100-lang/atc-spec/blob/main/CONTRIBUTING.md).

---

## Editors

- **Edison Flores** — AliceLabs LLC (co-creator, lead engineer)
- **Alejandro Flores** — AliceLabs LLC (co-creator, SDK & DX engineer)

Contact: `legal@alicelabs.site`

— 2026-08-19
