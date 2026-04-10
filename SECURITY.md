# Security Policy

This document describes vulnerability reporting and supported firmware lines for **MeshGrid-Node** firmware distributed through this repository. For licensing terms, see [`LICENSE`](LICENSE). For installation and hardware scope, see [`README.md`](README.md).

## Supported Versions

The MeshGrid-Node firmware is under active development. Security fixes, when issued, are provided for the **latest supported release line** unless a release note explicitly extends support for an older line.

| Version   | Supported |
| --------- | --------- |
| v0.0.3    | :white_check_mark: |
| v0.0.2    | :x: |
| v0.0.1    | :x: |
| < v0.0.1  | :x: |

Firmware artifacts may use tags such as `factory_v0.0.3`; treat **v0.0.3** as the supported release line for security-relevant updates.

## Reporting a Vulnerability

If you believe you have found a security vulnerability in MeshGrid-Node firmware or its documented distribution process, **do not** open a **public** GitHub issue for the **initial** report.

If this repository has **GitHub private security advisories** or **private vulnerability reporting** enabled, you may use that mechanism in addition to, or instead of, email, per GitHub’s instructions.

Otherwise, report security issues using:

- **Security contact:** [info@meshgrid.org](mailto:info@meshgrid.org)

Use a clear subject line (for example: `MeshGrid-Node security report`).

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

This policy applies to security issues affecting the supported firmware and documented stack, including but not limited to:

- vulnerabilities affecting confidentiality or integrity of mesh communications
- authentication, provisioning, or key-management weaknesses
- vulnerabilities that allow unauthorized access to node functionality
- remotely triggerable denial-of-service conditions
- security-impacting flaws in BLE, provisioning, or radio-facing logic

Use **GitHub Issues** only for **non-security** bugs, installation questions, feature requests, or general hardware compatibility (unless the maintainer asks you to move a report to the security contact).

### Out of scope

Reports that are typically **not** handled as security vulnerabilities here include: generic ESP32 platform issues without a MeshGrid-specific angle, third-party app store policies, and radio regulatory compliance (see `README.md` user responsibility sections).

## Disclosure Policy

Please do not publicly disclose a vulnerability (including proof-of-concept details that would enable exploitation) until the maintainers have had a reasonable opportunity to investigate and, where appropriate, prepare a fix or mitigation. Coordinated disclosure timelines are handled on a case-by-case basis; ask if you need a commitment window for publication.
