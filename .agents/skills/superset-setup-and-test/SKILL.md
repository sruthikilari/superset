---
name: superset-setup-and-test
description: Set up this Superset repository and run the right checks for a small change (frontend, dependency or Python), then report exactly what ran and what did not.
---

# Superset: set up, validate, report

Use this for small remediation changes (a dependency bump, a config default, a code-quality fix).

## Set up

**Frontend** (`superset-frontend/`)
- Needs Node `^24.16.0` and npm `^11.13.0` (see `engines` in `package.json`). If the default npm is older, use `npx --yes npm@11.13.0`.
- Install with `npx --yes npm@11.13.0 ci --no-audit --no-fund`. Do not use `npm install`; it can rewrite the lockfile.

**Backend**
- Python 3.11 or newer. Create a virtualenv and install `requirements/development.txt`.
- Run tools through the virtualenv, for example `.venv/bin/python -m pre_commit ...`.

## Validate

Run only what applies to the files you changed, and always the checks the issue names.

| Change | Run |
|---|---|
| Dependency or lockfile | `npm ls <package>` before and after; `npm ci`; the tests of a component that uses it; `npm run build` |
| Frontend code | `npm run test -- <path to the test>`; `npm run lint` |
| Python code | `pytest <path to the test>` (unit tests in `tests/unit_tests/`); `pre-commit run --files <changed files>` |
| Any change | `git diff --stat` and `git diff --check` |

`npm run type` can fail in a fresh checkout with TS6305, because generated chart-plugin declarations are missing. Run it on an unmodified checkout before blaming your change, and report the result either way.

## Change rules

- Make the smallest change that meets the acceptance criteria. No unrelated edits or formatting churn.
- For a lockfile change, verify with a diff that only the intended packages changed.
- Add a note to `UPDATING.md` if the change alters default behavior.

## Pull request

- Title in Conventional Commits form, for example `fix(config): drop hard-coded default SMTP credentials`.
- Follow `.github/PULL_REQUEST_TEMPLATE.md`.
- Report exactly what you ran, what passed, what failed, and what you could not run and why. Do not report a check as passed if you did not run it.
