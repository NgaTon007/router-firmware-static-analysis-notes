# Methodology and Reproducibility

## Review mode

The archive was reportedly extracted without modifying the original ZIP and reviewed using static-only methods. Firmware binaries and executables were not run, and no network requests were made during the described review.

## Artifact details

- Filename: `squashfs-root.zip`
- ZIP integrity: reported successful
- Entries: 4,892 files
- Extracted size: 153,628,827 bytes
- SHA-256: not supplied; add after computing locally
- Device model / firmware version: not yet independently verified
- Acquisition source and date: not supplied

## Evidence standards

Use the following distinctions in notes:

- **Observed:** directly supported by a path, source text, string, symbol, or recorded tool output.
- **Interpretation:** a reasoned explanation of observed evidence.
- **Hypothesis:** a plausible explanation that needs more evidence.
- **Confirmed vulnerability:** requires evidence of a security flaw and validation in an authorized, controlled environment.

Do not treat a symbol, string, filename, or endpoint as proof that a feature is reachable or exploitable.

## Suggested record for each new observation

- Finding ID
- Artifact/path and line number or address, where available
- Exact static method/tool and version
- Sanitized output or a short paraphrase
- Interpretation and confidence
- Alternative explanations
- Follow-up question

Never add private-key contents, credentials, password hashes, tokens, or unique device identifiers to notes.

## Scope limitations

These notes depend on the accuracy of the supplied observations. The exact firmware build, build configuration, runtime provisioning, and device state were not independently verified here. No dynamic execution, live endpoint testing, or network validation is claimed.
