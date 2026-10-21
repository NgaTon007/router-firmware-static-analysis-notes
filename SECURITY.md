# Security Policy and Responsible Research

## Authorized scope

This repository records static analysis for learning and defensive research. Inspect only firmware you own or are explicitly authorized to analyze. Do not use observations to access, disrupt, or modify systems without permission.

## Sensitive material

The extracted tree appears to contain paths associated with private keys, SSH host keys, password hashes, and device-key configuration. Do not commit the original archive, unreviewed filesystem, secret values, or complete sensitive configuration files.

If sensitive material is accidentally committed:
1. Restrict access while assessing exposure.
2. Remove the material from the repository and Git history; a later deletion commit alone is insufficient.
3. Rotate or revoke exposed credentials/keys where applicable.
4. Review repository collaborators, forks, downloads, and logs as appropriate.

## Reporting

If a potential vulnerability is found, validate it only in an authorized lab and report it privately to the responsible vendor or maintainer. Avoid publishing operational exploit instructions or secret material that could put users at risk.

## No guarantee

The notes are preliminary observations, not a certification, guarantee, or vendor-approved security assessment. The maintainer does not undertake to provide support, remediation, incident response, or fixes.
