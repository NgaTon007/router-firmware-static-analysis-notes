# Router Firmware Static Analysis

> **Research notes only.** This repository documents a static, non-executing review of an extracted embedded Linux firmware filesystem. It is written in a public-facing documentation style, but the repository is intended to remain private.

## Purpose

This project records filesystem observations, relevant source paths, and preliminary technical hypotheses from `squashfs-root.zip`. The archive was unpacked for inspection; the original archive was not modified. No firmware executables were run and no network requests were made as part of the described review.

The notes are intended for learning, defensive review, and reproducibility. They are not a vendor statement, formal security audit, or proof that a vulnerability exists.

## Scope and artifact summary

- Archive: `squashfs-root.zip`
- ZIP integrity: reported successful
- Archive entries: 4,892 files
- Extracted size: 153,628,827 bytes
- Observed content types: Lua, shell scripts, UCI configuration, MIPS ELF executables, shared libraries, web assets, TLS certificates/keys, and captive-portal/app-auth components
- Product/build identification: not independently verified in this repository

## Main observations

1. **WiFiDog secret handling:** `usr/local/lua/dev_sta/appAuthDefUrl.lua` appears to encrypt the `wifidogSecret` value before it is stored in UCI configuration. The value is not returned directly by the API described in the review.
2. **Configuration synchronization:** `app_auth_update.sh` appears to synchronize the `wifidogSecret` configuration from the `app_auth` side to the `webservice` side.
3. **Signing-related indicators:** strings and crypto symbols in `webservice.elf` indicate available HMAC/SHA-256 functionality and request formats containing `timestamp` and `sign`. This alone does not establish the exact signature algorithm or formula.
4. **WiFiDog request formats:** strings include `/auth/` request templates for checks, client status/traffic, counters, and a `v=2` variant.
5. **CHAP representation:** format strings are consistent with octal-escaped byte representation for `chap_id` and `chap_challenge`.
6. **Crypto library indicators:** `libsd_crypto.so` contains indicators associated with OpenSSL-style key derivation, AES-CBC, and Base64 encryption/decryption. The exact mode and key handling for a particular configuration field remain unverified.
7. **Local management interfaces:** strings reference local API and LuCI paths for login, mesh information, password changes, and configuration-related functions.
8. **TLS material:** paths resembling server certificates and private keys exist in the extracted tree. These must be treated as sensitive. Their relationship to portal signing is not established.

See [`docs/findings.md`](docs/findings.md) and [`docs/filesystem-map.md`](docs/filesystem-map.md) for detail.

## Limitations

This is a static review based on the observations recorded here. Strings, imported symbols, and configuration paths can suggest functionality but do not by themselves prove that a code path is reachable, enabled in a given build, or used at runtime. Exact firmware identity, build options, provisioning behavior, and runtime configuration have not been independently confirmed.

## Responsible use

- Analyze only firmware and devices you own or are authorized to assess.
- Do not use these notes to bypass access controls, forge portal requests, access accounts, or interfere with networks without explicit authorization.
- Do not publish secret values, private-key contents, password hashes, tokens, or device-specific identifiers.
- Keep the original firmware archive and unreviewed extracted tree out of version control unless you have confirmed authorization and redistribution rights.

## Disclaimer

This repository is provided **“as is”**, for informational and educational purposes only, without warranties or guarantees of accuracy, completeness, fitness for a particular purpose, or non-infringement. The author/maintainer accepts no responsibility or liability, to the maximum extent permitted by applicable law, for damage, loss, service interruption, security incidents, legal consequences, or other outcomes arising from use, misuse, interpretation, or reliance on this material. Users are solely responsible for determining whether their actions are lawful and authorized, validating findings independently, and obtaining appropriate professional advice where needed.

This notice does not waive rights or obligations that cannot legally be excluded. It is not legal advice.

## Repository contents

- `docs/filesystem-map.md` — notable paths and their observed roles
- `docs/findings.md` — observations, confidence levels, and unresolved questions
- `docs/methodology.md` — scope, procedure, and reproducibility notes
- `SECURITY.md` — sensitive-data and responsible-disclosure guidance
- `.gitignore` — common archive and secret-file exclusions

## License and redistribution

No license is provided because firmware ownership and redistribution terms have not been established. Keeping this repository private does not automatically authorize uploading third-party firmware or its contents.
