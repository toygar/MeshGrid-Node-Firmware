# Security Policy

## Supported Versions

MeshGrid-Node is currently in early-stage development. Security fixes, when issued, are applied only to the latest release branch or latest public release unless explicitly stated otherwise.

| Version   | Supported |
| --------- | --------- |
| v0.0.2    | :white_check_mark: |
| v0.0.1    | :x: |
| < v0.0.1  | :x: |

## Reporting a Vulnerability

If you believe you have found a security vulnerability in MeshGrid-Node, please do **not** open a public GitHub issue.

While this repository is private, or if private vulnerability reporting is not enabled, please report security issues using the following private contact method:

- **Security contact:** `info@meshgrid.org`

When submitting a report, please include:

- affected version
- hardware/board used
- steps to reproduce
- impact description
- proof-of-concept, logs, or captures if available
- any suggested mitigation or fix, if known

### What to expect

- We aim to acknowledge valid reports within **5 business days**
- We aim to provide a status update within **14 business days**
- If the issue is confirmed, we may work on a fix privately before public disclosure
- Once a fix is available, we may publish updated guidance or a public security notice

### Scope

This policy applies to security issues such as, but not limited to:

- vulnerabilities affecting confidentiality or integrity of mesh communications
- authentication, provisioning, or key-management weaknesses
- vulnerabilities that allow unauthorized access to node functionality
- remotely triggerable denial-of-service conditions
- security-impacting flaws in BLE, provisioning, or radio-facing logic

Please use normal GitHub issues for non-security bugs, installation problems, feature requests, or general hardware compatibility questions.

## Disclosure Policy

Please do not publicly disclose a vulnerability until we have had a reasonable opportunity to investigate and, where appropriate, prepare a fix or mitigation.
