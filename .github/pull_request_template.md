# Pull request

## Summary

<!-- What does this PR change, and why? One or two sentences is often enough. -->

## Linked issue

<!-- "Closes #123", or a link to the issue in another repo. Link related PRs in other repos too, and say which must merge first. -->

## Repos touched

<!-- Tick every repo this change (or its companion PRs) touches. -->

- [ ] main-server
- [ ] customer-server
- [ ] hoizr-worker
- [ ] tracking-server
- [ ] hoizr-shared
- [ ] hoizr-client
- [ ] business-client
- [ ] internal-admin-client
- [ ] hoizr-artist-client
- [ ] hoizr-scanner-app
- [ ] hoizr-walkthrough or .github (docs only)

## Type of change

- [ ] Bug fix
- [ ] New feature
- [ ] Refactor (no behaviour change)
- [ ] Documentation
- [ ] Tests
- [ ] Build, dependencies or tooling

## How was this tested?

<!-- Commands you ran, tests you added, and what you checked by hand. For UI changes, include the screen sizes you checked. -->

## Checklist

- [ ] `tsc` / `yarn build` / `flutter analyze` passes in every repo I touched, and `yarn lint` passes where ESLint is configured.
- [ ] I added or updated tests for any logic I changed.
- [ ] If a GraphQL schema or `.graphql` operation changed, I re-ran `yarn codegen` in the affected clients and committed the result.
- [ ] If I changed hoizr-shared, I listed the consumers that need a `@hoizr-technology/shared` version bump.
- [ ] I updated the README or the walkthrough if behaviour, setup or environment variables changed.
- [ ] No secrets, tokens, `.env` files or real customer data are committed.
- [ ] For UI changes, I attached before and after screenshots.

## Screenshots

<!-- Before and after, for UI changes. Delete this section otherwise. -->
