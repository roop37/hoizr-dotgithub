# Security policy

Hoizr handles payments, personal data and ticket entry, so we take security reports seriously. Thank you for helping keep the project and its users safe.

## Supported versions

Hoizr does not publish versioned releases of its services. Security fixes land on the default branch.

| Component | Supported |
|---|---|
| Latest `main` of every Hoizr repository listed in the [walkthrough](https://github.com/roop37/hoizr-walkthrough#the-repositories) | Yes |
| Latest published version of `@hoizr-technology/shared` | Yes |
| Older commits, forks and earlier package versions | No |

## Reporting a vulnerability

**Please do not report security vulnerabilities through public issues, discussions or pull requests.**

Use one of these private channels:

1. **GitHub private vulnerability reporting (preferred).** Open the affected repository, go to the **Security** tab and choose **Report a vulnerability**. This button appears only on repositories where private vulnerability reporting is enabled; if you do not see it, use email.
2. **Email** [contact@hoizr.com](mailto:contact@hoizr.com) with a subject line that starts with `[SECURITY]`, for example `[SECURITY] customer-server: <short summary>`.

If you are unsure whether something is a security issue, report it privately anyway.

## What to include

The more of this you can share, the faster we can confirm and fix the problem:

- The affected repository, plus the file, route or GraphQL operation if you know it.
- The commit hash or date of the code you tested.
- The type of issue (for example injection, broken access control, information disclosure).
- Step-by-step instructions to reproduce it against a local setup.
- A proof of concept, if you have one.
- The impact as you understand it: what an attacker could do and what they would need first.
- Any suggested fix or mitigation.
- Whether and how you would like to be credited.

## What to expect

These are goals, not guarantees, for a small team:

- **Acknowledgement within 3 business days** of your report.
- **An initial assessment** (confirmed, needs more information, or not a vulnerability) as soon as we have reproduced it.
- **Regular updates** while we work on a fix.
- **Coordinated disclosure.** We ask that you keep the details private until a fix is available on `main`. We will agree a disclosure date with you and, where it fits, publish a GitHub security advisory.
- **Credit** in the advisory or release notes if you want it.

## Scope

**In scope:** the source code in every Hoizr repository listed in the [walkthrough](https://github.com/roop37/hoizr-walkthrough#the-repositories).

**Out of scope:**

- Vulnerabilities in third-party services that Hoizr integrates with (payment, messaging, email, media storage, maps and sign-in providers). Report those to the provider directly.
- Vulnerabilities in third-party dependencies that are already publicly known, unless you can show a Hoizr-specific way to exploit them.
- Denial-of-service, load or volumetric testing.
- Social engineering, phishing or physical attacks against Hoizr staff, venues or users.
- Reports produced only by automated scanners without a demonstrated impact.

## Safe harbour

We will not pursue or support legal action against anyone who researches and reports a vulnerability in good faith and in line with this policy. Good faith means that you:

- Test against your own local deployment of the open-source code, not against hoizr.com, business.hoizr.com, api.hoizr.com or any other live Hoizr service.
- Do not access, change or delete data that does not belong to you.
- Do not disrupt the service or degrade it for other people.
- Report the issue to us privately and give us reasonable time to fix it before any public disclosure.

If you are unsure whether an activity is covered, ask us first at [contact@hoizr.com](mailto:contact@hoizr.com).

## Bug bounty

Hoizr does not currently run a paid bug bounty program. We are grateful for every report and are happy to credit researchers publicly.
