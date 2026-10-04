# Contributing to Hoizr

Thanks for your interest in Hoizr. Bug reports, fixes, tests, docs and new features are all welcome, whether you are fixing a typo or reworking a service. This guide applies to every Hoizr repository listed in the [walkthrough](https://github.com/roop37/hoizr-walkthrough#the-repositories) unless a repository has its own `CONTRIBUTING.md`.

Hoizr is pre-launch software built by a very small team. Some parts are well covered and some are rough. Where this guide points out a gap, treat it as an open invitation.

## Contents

- [How the project is organised](#how-the-project-is-organised)
- [Prerequisites](#prerequisites)
- [Local setup](#local-setup)
- [Finding something to work on](#finding-something-to-work-on)
- [Workflow](#workflow)
- [Coding conventions](#coding-conventions)
- [Testing](#testing)
- [Documentation](#documentation)
- [Pull request checklist](#pull-request-checklist)
- [Code of conduct and security](#code-of-conduct-and-security)
- [License](#license)

## How the project is organised

Hoizr is a multi-repo project. Each repository is an independent package with its own lockfile, and there is no workspace root. Pick the repository that owns the behaviour you want to change:

| If you want to change... | Work in | Package manager |
|---|---|---|
| Organizer, admin or artist GraphQL operations | [main-server](https://github.com/roop37/main-server) | npm |
| Customer auth, cart, checkout, payment webhooks, scanner API | [customer-server](https://github.com/roop37/customer-server) | npm |
| Emails, SMS, WhatsApp, campaigns, order finalisation, scheduled jobs | [hoizr-worker](https://github.com/roop37/hoizr-worker) | pnpm |
| Analytics ingest | [tracking-server](https://github.com/roop37/tracking-server) | npm |
| Database schemas, enums, queue names, ledger helpers used by the backends | [hoizr-shared](https://github.com/roop37/hoizr-shared) | npm |
| The customer storefront | [hoizr-client](https://github.com/roop37/hoizr-client) | Yarn 1 |
| The organizer dashboard or the business.hoizr.com marketing site | [business-client](https://github.com/roop37/business-client) | Yarn 1 |
| The internal operations console | [internal-admin-client](https://github.com/roop37/internal-admin-client) | Yarn 1 |
| The artist dashboard | [hoizr-artist-client](https://github.com/roop37/hoizr-artist-client) | Yarn 1 |
| The door check-in app | [hoizr-scanner-app](https://github.com/roop37/hoizr-scanner-app) | Flutter (`pub`) |

Use the package manager the repository's lockfile was made with. Mixing them produces a second lockfile and drifting dependency trees. If you are unsure where a change belongs, open an issue first and ask.

For a tour of how these pieces fit together, start with the [hoizr-walkthrough](https://github.com/roop37/hoizr-walkthrough).

## Prerequisites

- **Node.js 20 or newer.** The Docker images use Node 23.
- **npm, pnpm and Yarn 1 (classic)**, depending on the repositories you work in (see the table above). `corepack enable` provides the Yarn version pinned in `packageManager`.
- **MongoDB running as a replica set.** The backends use multi-document transactions (for example in [order.service.ts](https://github.com/roop37/customer-server/blob/main/src/modules/order/service/order.service.ts)), which fail on a standalone `mongod`. A local single-node replica set works.
- **Redis.** All four backends use it for BullMQ queues; the API servers also keep rate limits and refresh tokens there, and customer-server keeps cart holds.
- **A GitHub personal access token** with the `read:packages` scope, to install the shared package (see below).
- **Flutter SDK** for the scanner app. `pubspec.yaml` requires Flutter 3.22 or newer; a current stable release is the safest choice.

## Local setup

Follow the [local development guide](https://github.com/roop37/hoizr-walkthrough/blob/main/docs/09-local-development.md) in the walkthrough. It covers start order, environment variables, seed data and codegen for every repository.

### Installing `@hoizr-technology/shared`

main-server, customer-server, hoizr-worker and tracking-server depend on `@hoizr-technology/shared`, which is published to [GitHub Packages](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry). GitHub Packages requires an access token even for public packages, so a fresh clone cannot install without one.

1. Create a [classic personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) with the `read:packages` scope.
2. Export it in your shell as `GITHUB_TOKEN`.
3. Add these two lines to your user-level `~/.npmrc`, so they apply to every repository (pnpm reads the same file):

```ini
@hoizr-technology:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${GITHUB_TOKEN}
```

Never commit a token. Most backends ignore `.npmrc` in Git; customer-server tracks one that holds only the registry line.

If you are changing the shared package itself, you can work against a local build instead. Clone `hoizr-shared` next to the backends and run `npm run build`. main-server and customer-server ship a `tsconfig.verify.json` that type-checks against `../hoizr-shared/dist`:

```bash
npx tsc -p tsconfig.verify.json --noEmit
```

You can also point a backend at the local package with a `file:` link. Be aware that this can load two copies of `graphql`, `type-graphql`, `mongoose` and `@typegoose/typegoose`, which breaks schema building with `instanceof` errors. main-server, customer-server and hoizr-worker run `scripts/dedupe-shared-deps.js` before `dev`, `build` and `start` to symlink those packages back to a single copy ([main-server's version](https://github.com/roop37/main-server/blob/main/scripts/dedupe-shared-deps.js)). Do not commit a `file:` dependency.

## Finding something to work on

- Issues labelled [`good first issue`](https://github.com/search?q=user%3Aroop37+label%3A%22good+first+issue%22+state%3Aopen&type=issues) are scoped for a first contribution.
- Issues labelled [`help wanted`](https://github.com/search?q=user%3Aroop37+label%3A%22help+wanted%22+state%3Aopen&type=issues) are ones the maintainers would like help with.
- Comment on an issue before you start, so two people do not build the same thing.
- For anything larger than a bug fix (a new feature, a schema change, a new dependency), open an issue to agree on the approach before writing code.

## Workflow

1. **Fork** the repository and clone your fork.
2. **Branch** from `main` with a short descriptive name: `fix/checkout-total-rounding`, `feat/guestlist-csv-export`, `docs/worker-env-vars`.
3. **Keep pull requests small.** One logical change per PR is easier to review and to revert. Split refactors from behaviour changes.
4. **Write commit messages in the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) style** the history already uses: `type(scope): summary`, for example `fix(waitlist): enforce purchase gate at createOrder`. Common types are `feat`, `fix`, `refactor`, `docs`, `test` and `chore`.
5. **Open the pull request** against `main` and fill in the template.

### Changes that span repositories

A feature often touches more than one repository: a shared schema, a server resolver and a client screen. Open one PR per repository, link them to each other and to the issue, and say in each description which order they must merge in. The usual order is hoizr-shared, then the servers and worker, then the web apps.

## Coding conventions

Match the code around you. When in doubt, copy the structure of the nearest sibling module.

### TypeScript

TypeScript strictness differs between repositories. Work within each repository's current settings; do not loosen them, and avoid adding new `any` or `as any` casts.

| Setting | Repositories |
|---|---|
| `strict: true` | hoizr-worker, tracking-server, hoizr-client, hoizr-artist-client |
| `noImplicitAny` without full `strict` | main-server, customer-server, hoizr-shared |
| `strictNullChecks` only | business-client, internal-admin-client |

Moving a repository to full `strict` mode is a welcome contribution, best done one module at a time.

### Server module structure

main-server and customer-server group code by domain under `src/modules/<domain>/`:

| Folder | Holds |
|---|---|
| `interface/` or `interfaces/` | GraphQL input and object types, local enums and types |
| `schema/` | The Mongoose model, built with `getModelForClass()` from a class in `@hoizr-technology/shared` |
| `resolver/` | The TypeGraphQL resolver class with `@Query` and `@Mutation` methods |
| `service/` | Business logic that resolvers call |

Folder names vary slightly between modules (singular and plural); follow whatever the neighbouring modules use. Register every new resolver in `src/resolvers/index.resolver.ts` ([main-server](https://github.com/roop37/main-server/blob/main/src/resolvers/index.resolver.ts), [customer-server](https://github.com/roop37/customer-server/blob/main/src/resolvers/index.resolver.ts)). See the [coupon module](https://github.com/roop37/main-server/tree/main/src/modules/coupon) for a compact example.

hoizr-worker groups each job type under `src/workers/<domain>/`, and queue names come from `QueueNames` in [hoizr-shared](https://github.com/roop37/hoizr-shared/blob/main/src/queue/queue.ts). Add new queue names there instead of hard-coding strings.

### GraphQL codegen

The four web apps call the servers through a typed SDK generated by [GraphQL Code Generator](https://the-guild.dev/graphql/codegen/docs/getting-started). If you change a server's GraphQL schema or a client's `.graphql` operation files, start the server locally and run `yarn codegen` in each affected client, then commit the regenerated file.

| Client | Schema source | Generated file |
|---|---|---|
| business-client | main-server, `http://localhost:4000/graphql` | `generated/graphql.ts` |
| internal-admin-client | main-server, `http://localhost:4000/graphql` | `generated/graphql.ts` |
| hoizr-artist-client | main-server, `http://localhost:4000/graphql` | `src/generated/graphql.ts` |
| hoizr-client | customer-server, `http://localhost:4001/graphql` | `src/generated/graphql.ts` |

Never edit a generated file by hand.

### Changing the shared package

Schema classes, enums and queue names live in hoizr-shared and reach the backends as a published package. A change there takes several steps:

1. Open a PR in hoizr-shared with the change. Run `npm run build` to make sure it compiles.
2. Test it against the consuming backends locally (see [Local setup](#local-setup)).
3. After merge, a maintainer bumps the `version` in `package.json` and publishes to GitHub Packages. Publishing requires write access, so contributors do not need to do this step.
4. Open follow-up PRs that bump `@hoizr-technology/shared` in each consumer that needs the change: main-server, customer-server, hoizr-worker and tracking-server.
5. If GraphQL types changed, regenerate the SDK in the affected web apps.

Add new fields as optional where you can, so consumers on the previous version keep working while the bumps roll out.

### Flutter

Run `flutter analyze` before you push. The scanner uses the default `flutter_lints` rules.

### Secrets and configuration

Never commit `.env` files, tokens, keys or real customer data. When you add an environment variable, add its name (no value) to the repository's `.env.example`, and to the Zod schema in `src/utils/environment.ts` in main-server or customer-server.

## Testing

Test coverage is uneven today, and improving it is one of the most useful things you can do.

- **There is no shared test framework.** Tests are plain scripts that use `node:assert` (some use `node:test`). They mostly cover pure functions such as pricing math, ledger entries and permission checks.
- **Backends:** tests live in `src/tests/` or next to the code as `*.test.ts`. Run one with `npx ts-node --transpile-only <path>`.
- **hoizr-shared:** tests live in `scripts/*.test.js` and import from `dist/`, so run `npm run build` first, then `node <path>`.
- **Web apps:** hoizr-client and hoizr-artist-client have a few `yarn test:*` scripts. business-client has one `node:test` file. internal-admin-client and tracking-server have no tests yet.
- **Scanner app:** the only test is the default Flutter template and needs replacing.
- **No CI runs tests, lint or type checks on pull requests yet.** Run the checks yourself before you push.

What we ask of contributors:

- If you change logic (money, inventory, permissions, dates, parsing), add a test that fails without your change. A small assert script in the same style as its neighbours is enough.
- Before you open a PR, run the build for each repository you touched. For the backends and hoizr-shared, `npm run build` (or `pnpm build` in hoizr-worker) runs `tsc`. For the web apps, `yarn build` runs the type check and `yarn lint` runs ESLint.
- ESLint is configured in business-client and internal-admin-client. hoizr-client and hoizr-artist-client have a `lint` script but no committed config yet; adding one is a good first contribution.
- For UI changes, check the page at phone width as well as desktop.

## Documentation

- If your change alters behaviour, setup steps, environment variables or a public API, update the repository's README in the same PR.
- If it changes how services interact (a new queue, webhook or cross-service call), also open a PR in [hoizr-walkthrough](https://github.com/roop37/hoizr-walkthrough) so the system tour stays accurate.
- Write plainly and link to the code you describe.

## Pull request checklist

Before you request a review, check that:

- [ ] The PR does one thing and links the issue it addresses.
- [ ] `tsc`, `yarn build` or `flutter analyze` passes in every repository you touched, and `yarn lint` passes where ESLint is configured.
- [ ] Logic changes come with a test.
- [ ] GraphQL codegen was re-run and committed if a schema or operation changed.
- [ ] hoizr-shared changes note which consumers need a version bump.
- [ ] READMEs and the walkthrough are updated if behaviour or setup changed.
- [ ] No secrets, tokens, `.env` files or real customer data are committed.
- [ ] UI changes include before and after screenshots.

A maintainer will review your PR, may ask for changes, and will merge it once it is ready.

## Code of conduct and security

Everyone taking part in Hoizr spaces is expected to follow the [Code of Conduct](https://github.com/roop37/hoizr-dotgithub/blob/main/CODE_OF_CONDUCT.md).

Do not report security vulnerabilities in public issues. Follow the [security policy](https://github.com/roop37/hoizr-dotgithub/blob/main/SECURITY.md) instead.

For questions and help, see [SUPPORT.md](https://github.com/roop37/hoizr-dotgithub/blob/main/SUPPORT.md).

## License

Hoizr is released under the [MIT License](https://github.com/roop37/hoizr-dotgithub/blob/main/LICENSE). By submitting a contribution, you agree that it is licensed under the MIT License as well.
