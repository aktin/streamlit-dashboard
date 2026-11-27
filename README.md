# Streamlit Dashboard

[![Verify](https://github.com/aktin/streamlit-dashboard/actions/workflows/verify.yml/badge.svg)](https://github.com/aktin/streamlit-dashboard/actions/workflows/verify.yml)
[![CodeQL](https://github.com/aktin/streamlit-dashboard/actions/workflows/scan-codeql.yml/badge.svg)](https://github.com/aktin/streamlit-dashboard/actions/workflows/scan-codeql.yml)
[![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/aktin/streamlit-dashboard/badge)](https://securityscorecards.dev/viewer/?uri=github.com/aktin/streamlit-dashboard)
[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](./LICENSE)

# Dashboard for AKTIN DWHs

## Stack

uv-managed Python package (src layout), ruff for linting and formatting, mypy strict, pytest with coverage gate, lefthook with shared hooks from aktin-github-scripts, and a hardened GitHub Actions pipeline (CI, CodeQL, Scorecard, dependency review).

## Getting started

Prerequisites: [uv](https://docs.astral.sh/uv/) on PATH (it installs the interpreter pinned in `.python-version`). The shared git hooks need `python3`, `gitleaks`, `actionlint`, `hadolint` and `shellcheck` on PATH. A missing tool fails the commit. actionlint, hadolint and shellcheck are only called when a workflow file, Dockerfile or shell script is staged.

```sh
uv sync                  # deps + venv
uv run pytest            # unit tests with coverage
uv run mypy              # type check
uv run ruff check .      # lint
uv run ruff format .     # format
uv build                 # sdist + wheel
uv run lefthook install  # git hooks (one-time)
```

The coverage report lands in `htmlcov/index.html`.

Commits must follow [Conventional Commits](https://www.conventionalcommits.org), enforced by the commit-msg hook:

```
<type>(<optional scope>): <subject>
```

Allowed types are `feat fix docs style refactor perf test chore`, the subject starts lowercase and has no trailing period, every line of the message stays at most 100 characters.

## Repository settings

The repository was created with `new-repo.sh` from [aktin-github-scripts](https://github.com/aktin/aktin-github-scripts) which also applied the settings profile `no-release.json`. To apply it again, run from the root of aktin-github-scripts:

```sh
repo-settings/apply.sh aktin/streamlit-dashboard repo-settings/no-release.json
```

GitHub asks the code owners in `.github/CODEOWNERS` for a review of every pull request into `main`. The merge does not wait for it.

## How the infra works

| Component | What it does |
| --- | --- |
| uv | Package and environment manager, single lockfile |
| ruff | Owns linting and formatting |
| mypy strict | Type checks `src` and `tests` |
| pytest + coverage | Unit tests with an 80% line gate (`fail_under`) |
| Pinned Python | Interpreter version fixed in `.python-version`, floor in `requires-python` |
| lefthook | All hooks come from [aktin-github-scripts](https://github.com/aktin/aktin-github-scripts#git-hooks), see `lefthook.yml`. pre-commit formats and lints staged Python files, scans for secrets with gitleaks, lints staged workflows, Dockerfiles and shell scripts and rejects data files and large files, commit-msg enforces Conventional Commits and rejects oversized commits, pre-push runs `uv run mypy`, `uv run pytest` and `uv build` |
| GitHub workflows and rules | Checks on pull requests, CodeQL, dependency review, Scorecard, Dependabot and the ruleset on `main`. Details in [.github/workflows/README.md](.github/workflows/README.md) |

## Project structure

```
src/streamlit_dashboard/  the package
tests/               pytest suite
.github/workflows/   verify, CodeQL, dependency review, Scorecard, described in the README there
```

## License

Apache-2.0. See [LICENSE](./LICENSE) and [NOTICE](./NOTICE).
