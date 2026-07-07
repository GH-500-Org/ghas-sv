# Security Policy

> 👋 **For students:** this file is the third GHAS demo artifact. It tells
> GitHub — and any human visitor — how to privately report a security problem
> in this project. When it is present, GitHub shows a **"Report a
> vulnerability"** button under the **Security** tab.

## Supported versions

This repository is a teaching sandbox. It is **intentionally vulnerable** and
is not deployed anywhere. Only the `main` branch is "supported" in the sense
that fixes will be applied there.

| Version | Supported |
| ------- | --------- |
| `main`  | ✅        |
| Any other branch or fork | ❌ |

## Reporting a vulnerability

If you believe you have found a **real** security issue in this repository
(as opposed to one of the seeded demo issues in `src/insecure.js` or
`package.json`), please report it privately. **Do not open a public issue.**

### Preferred: GitHub Private Vulnerability Reporting

1. Go to the [**Security**](../../security) tab of this repository.
2. Click **Report a vulnerability**.
3. Fill out the form with:
   - a clear description of the issue,
   - the file(s) and line(s) affected,
   - steps to reproduce,
   - the impact (what an attacker could do),
   - any suggested fix.
4. Submit. Only repository maintainers can see the report.

### Alternative

If for some reason you cannot use private vulnerability reporting, contact the
repository owner directly via their GitHub profile.

## What to expect

- **Acknowledgement:** within 5 business days.
- **Initial assessment:** within 10 business days — we will confirm whether we
  consider the report a vulnerability and, if so, its severity.
- **Fix + disclosure:** once a fix is available we will publish a GitHub
  Security Advisory crediting you (unless you prefer to remain anonymous).

## Scope

**In scope**

- Vulnerabilities in code committed to this repository.
- Vulnerabilities in the GitHub Actions workflows in `.github/workflows/`.

**Out of scope**

- The intentionally weak crypto in `src/insecure.js` — this is a teaching
  example.
- The intentionally outdated dependencies pinned in `package.json` — these
  drive the Dependabot demo.
- Findings against third-party services this project links to but does not
  operate (e.g. `jsonplaceholder.typicode.com`).

## Safe-harbour

Good-faith security research performed in accordance with this policy is
welcomed. Please do not:

- access, modify, or delete data that is not your own,
- run automated scans that degrade the service,
- publicly disclose the issue before a fix is available.

Thank you for helping keep this project — and its learners — safe. 🙏
