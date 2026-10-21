# Findings and Open Questions

## Summary

The archive review provides evidence of a WiFiDog/app-auth implementation, configuration handling for a field named `wifidogSecret`, request-format strings containing `timestamp` and `sign`, and available HMAC/SHA-256-related crypto symbols. It does **not** establish the exact signing formula or the actual runtime secret.

## F-001 — `wifidogSecret` configuration handling

- **Status:** Preliminary observation
- **Evidence path:** `usr/local/lua/dev_sta/appAuthDefUrl.lua`
- **Related path:** `app_auth_update.sh`
- **Observation:** The reviewed source flow appears to encrypt `wifidogSecret` before saving it to UCI configuration. The API behavior described in the review returns a presence indicator rather than the plaintext secret. The update script appears to synchronize the value between `app_auth` and `webservice` configuration.
- **Interpretation:** The secret may be provisioned at runtime or by a cloud/configuration process rather than stored in plaintext in the inspected default configuration.
- **Limitations:** The actual runtime value, provisioning source, and all call paths were not established.

## F-002 — Signing-related functionality in `webservice.elf`

- **Status:** Strong indicator; formula unconfirmed
- **Evidence artifact:** `webservice.elf`
- **Indicators reported:** `HMAC`, `EVP_sha256`, `SHA256_Init`, `SHA256_Update`, `SHA256_Final`, plus strings containing `timestamp` and `sign`.
- **Interpretation:** The binary appears to include HMAC/SHA-256 capability and request formats that carry a timestamp and signature field.
- **Not established:** Whether the particular WiFiDog signature is HMAC-SHA256; canonical input; parameter ordering; URL-decoding rules; key bytes; prefix/suffix; output encoding; or whether the key is directly `wifidogSecret`.
- **Caution:** Imported symbols and strings alone do not prove the exact implementation or a vulnerability.

## F-003 — WiFiDog request templates

- **Status:** Preliminary observation
- **Evidence artifact:** `webservice.elf`
- **Observation:** Strings include request templates for `stage=check`, client status/traffic, counters, and a variant containing `v=2`.
- **Interpretation:** Different request variants may serve different purposes or compatibility paths.
- **Open question:** Whether the timestamp/sign variant is newer or used under specific provisioning/runtime conditions has not been established.

## F-004 — CHAP challenge representation

- **Status:** Plausible interpretation
- **Evidence artifact:** `webservice.elf`
- **Observation:** Format strings resemble octal-escaped byte output for `chap_id` and `chap_challenge`.
- **Interpretation:** The challenge may be represented as escaped bytes rather than a simple hexadecimal string.
- **Open question:** Exact URL encoding, decoding, and canonicalization behavior.

## F-005 — `libsd_crypto.so` capabilities

- **Status:** Preliminary observation
- **Evidence artifact:** `libsd_crypto.so`
- **Indicators reported:** `EVP_BytesToKey`, `EVP_md5`, AES-128-CBC, AES-256-CBC, `Salted__`, Base64 helpers, and `sd_crypto_buf_encrypt` / `sd_crypto_buf_decrypt`.
- **Interpretation:** The library appears to contain encryption/decryption helpers compatible with these crypto primitives or formats.
- **Not established:** Which algorithm, passphrase, salt handling, or derivation path is used for the `wifidogSecret` field. Firmware-upgrade payload encryption and configuration-secret encryption must not be assumed to use identical settings.

## F-006 — Local management endpoints

- **Status:** Endpoint strings observed
- **Evidence artifacts:** Local API/LuCI and webservice-related strings
- **Observation:** Paths include login, mesh information, password change, Redis configuration, and WiFiDog/local-auth handlers.
- **Interpretation:** These strings suggest local management and gateway handler functionality.
- **Caution:** An endpoint string is not proof of reachability, authorization failure, or exploitability.

## F-007 — TLS and other sensitive files

- **Status:** Sensitive-data handling concern
- **Evidence paths:** See `docs/filesystem-map.md`.
- **Observation:** The extracted tree reportedly includes TLS certificate/private-key paths, SSH host-key paths, a device-key configuration, and `/etc/shadow`.
- **Interpretation:** The unreviewed archive should be treated as potentially containing secrets.
- **Caution:** TLS keys are not automatically portal signing keys. Do not publish their contents.

## Conclusions by confidence

### Supported by the recorded observations
- The filesystem includes WiFiDog/app-auth-related components.
- `wifidogSecret` is a configuration field in the reviewed flow.
- The binary contains request-format strings with `timestamp` and `sign`.
- HMAC/SHA-256-related symbols are present in the reviewed binary.
- Crypto-library indicators and sensitive file paths are present.

### Plausible, but not proven
- The portal signature may be HMAC-SHA256-based.
- The signing key may be related to `wifidogSecret`.
- The webservice may calculate signatures at runtime.
- Timestamp handling may be intended to reduce replay risk.
- A region-specific portal hostname may come from runtime/cloud configuration.

### Not established
- Exact signature formula and canonicalization.
- Actual `wifidogSecret` value.
- Whether any reported endpoint has a vulnerability.
- Whether default TLS keys are shared across devices or unique per device.
- Whether the inspected files match a particular currently deployed firmware build.

## Recommended next steps

1. Verify firmware model/build identifiers from non-sensitive version metadata.
2. Record SHA-256 of the original archive and tool versions.
3. Correlate each finding with exact source lines or disassembly addresses in a private working copy; do not publish secrets.
4. Keep signature-algorithm questions as unresolved unless the implementation can be demonstrated from static evidence.
5. If a credible vulnerability is confirmed, validate only in an authorized lab and use responsible disclosure.
