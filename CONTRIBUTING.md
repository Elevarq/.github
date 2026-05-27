# Contributing to Elevarq projects

This is the organization-wide default contribution guide. Individual
repositories may publish their own `CONTRIBUTING.md` with project-specific
build and test instructions, which takes precedence over this one.

## Change workflow

Elevarq follows an issue-first workflow:

1. **Open an issue** describing the change and the problem it solves.
   The issue is the durable record of what is being changed and why.
2. **Branch** off the latest `main`, named after the issue
   (e.g. `123-short-slug`).
3. **Make the change** on the branch. Keep pull requests small and
   scoped to a single issue.
4. **Open a pull request** that references the issue
   (`closes #123`) so it is linked and auto-closed on merge.

## Specification-led development

Behavioural changes start from a specification and its tests, not from
implementation. If you are proposing new behaviour, describe the intended
behaviour and acceptance criteria in the issue first. Trivial fixes
(typos, formatting, documentation) do not require a specification.

## Before you commit

Run the repository's local checks (formatting, vet/lint, tests, and the
security gates) before pushing. Most repositories ship a preflight script
or `make` targets for this; see the repository's own `CONTRIBUTING.md`.

## Reporting security issues

Do not open public issues for vulnerabilities. See
[SECURITY.md](SECURITY.md).
