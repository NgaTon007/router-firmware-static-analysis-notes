# Filesystem Map

This page records paths mentioned during the static review. Paths are relative to the extracted firmware root unless otherwise stated. Descriptions are preliminary and should be confirmed against the exact firmware build.

## WiFiDog app-auth and secret configuration

| Path / artifact | Recorded observation | Confidence / caveat |
|---|---|---|
| `usr/local/lua/dev_sta/appAuthDefUrl.lua` | Handles `wifidogSecret`; the reviewed flow appears to encrypt it before configuration storage and avoid returning the plaintext value directly in the API response. | Based on reviewed source flow; exact runtime behavior depends on build and call path. |
| `app_auth_update.sh` | Appears to synchronize `wifidogSecret` from the `app_auth` configuration to `webservice`. | Confirm the exact installed path and script version before making build-specific claims. |
| `/etc/config/app_auth` | Reported default UCI configuration; the review did not find a plaintext secret field in this file. | Runtime provisioning may differ. |
| `/etc/config/webservice` | Reported destination for the synchronized configuration value. | The actual runtime value was not established. |

## Binaries and crypto library

| Path / artifact | Recorded observation | Confidence / caveat |
|---|---|---|
| `webservice.elf` | Strings include request templates with `timestamp` and `sign`, `/auth/` endpoint variants, and CHAP formatting indicators. | Strings do not prove a particular path executes or establish the full signature formula. |
| `app_auth.elf` | Identified in the archive as an app-auth component. | Detailed behavior should be documented only where supported by additional static evidence. |
| `libsd_crypto.so` | Contains indicators including `EVP_BytesToKey`, `EVP_md5`, AES-128-CBC, AES-256-CBC, `Salted__`, Base64 helpers, and `sd_crypto_buf_encrypt` / `sd_crypto_buf_decrypt`. | These indicators do not establish which mode, key, or derivation is used for a specific field. |

## Request-format indicators found in `webservice.elf`

The following are paraphrased/selected string patterns from the review, not complete protocol specifications:

- Check request: `/auth/?gw_sn=...&gw_id=...&stage=check`
- Client status/traffic request: fields may include `stage`, `ip`, `mac`, `token`, `incoming`, `outgoing`, `logout`, `kickoff`, `dev_type`, `ls_incoming`, and `ls_outgoing`.
- Counters request: fields may include `stage=counters_2`, `total`, `seq`, `ts`, and `macList`.
- Versioned status request: fields may include `ssid` and `v=2`.
- Signing-related formats: strings include `timestamp` and `sign` in request-format templates.

The observed strings are consistent with multiple request types. They do not prove that a legacy `v=2` request and a timestamp/sign request have the same purpose or signing rules.

## CHAP formatting

`webservice.elf` includes format strings consistent with percent-encoded backslash sequences and octal escapes for `chap_id` and `chap_challenge`. This suggests an escaped-byte representation, but exact decoding and canonicalization behavior has not been independently verified.

## Local management paths

The review recorded strings resembling these local endpoints:

- `/api/v1/login/`
- `/api/v1/interface/get_mesh_info/`
- `/api/v1/interface/set_mesh_info/`
- `/api/v1/user/change_password/`
- `/api/v1/public/redis/edit_redis_ip/`
- `/cgi-bin/luci/api/cmd`
- `/cgi-bin/luci/`
- `/wifidog/auth`
- `/wifidog/openAuth`
- `/wifidog/online/`
- `/wifidog/query/`
- `/auth/wxwifiAuth/logon`
- `/ga_usrpwdauth`
- `/ga_qrcode_active_auth`
- `/ga_qrcode_passive_auth`
- `/upload_authlog`

These appear to be local gateway or management handlers based on their names. They are not, by themselves, evidence of a remotely exploitable vulnerability.

## Default portal configuration and certificates

Reported configuration indicators include `maccauth.ruijie.com.cn` and an HTTP path resembling `/auth/wifidogAuth/login/`. The review also recorded a configurable `ad_url` used to construct service paths such as `/auth/servlet/standardAuthServlet` and `/auth/wifidogAuth`. The observed region-specific hostname may therefore be runtime/cloud-provisioned, but this remains a hypothesis until confirmed from the relevant code and device configuration.

Sensitive paths reported in the extracted tree include:

- `/etc/lighttpd/server.pem`
- `/etc/cert/auth/default_cert/key.pem`
- `/etc/cert/auth/default_cert/cert.pem`
- `/etc/dropbear/dropbear_rsa_host_key`
- `/etc/easyson/ca/1024/server.key`
- `/etc/easyson/ca/2048/server.key`
- `/etc/config/device_key`
- `/etc/shadow`

Do not copy private-key contents, password hashes, device keys, or unique device identifiers into this repository. The existence of TLS private keys does not establish that they are WiFiDog signing secrets.
