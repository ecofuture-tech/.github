# Organization Workflows

This repository contains reusable GitHub Actions workflows for the EcoFuture Technology Services LLC organization.

## Available Workflows

### 1. Update licenses (`reusable-update-licenses.yml`)

Automatically updates license files and headers before release.

**Features:**
- Generates `THIRD_PARTY_LICENSES.md` using `pip-licenses`
- Adds Apache 2.0 license headers to all Python files using `addlicense`
- Commits changes automatically with `[skip ci]` tag

**Usage in your repository:**
```yaml
# .github/workflows/update-licenses.yml
name: Update Licenses

on:
  push:
    branches:
      - main
    paths:
      - '**.py'
      - 'pyproject.toml'

jobs:
  pre-release:
    uses: ecofuture-tech/.github/.github/workflows/reusable-update-licenses.yml@main
    with:
      python-version: '3.14'  # optional, default: '3.12'
      copyright-holder: 'ecofuture-tech'  # optional, default: 'ecofuture-tech'
      copyright-year: '2026'  # optional, default: '2026'
```

### 2. Build and Publish (`reusable-build-publish.yml`)

Builds and publishes Python packages to PyPI.

**Features:**
- Extracts version from git tags (format: `v1.0.0`, `v1.0.0-rc1`, etc.)
- Verifies license files and headers exist
- Builds package using `python -m build`
- Publishes to Test PyPI for pre-releases (optional)
- Publishes to PyPI for stable releases
- Creates GitHub releases automatically

**Usage in your repository:**
```yaml
# .github/workflows/build-and-publish.yml
name: Build and Publish

on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  build:
    uses: ecofuture-tech/.github/workflows/reusable-build-publish.yml@main
    with:
      python-version: '3.12'  # optional
      create-github-release: true  # optional, default: true
    secrets:
      PYPI_TOKEN: ${{ secrets.PYPI_TOKEN }}
      TEST_PYPI_TOKEN: ${{ secrets.TEST_PYPI_TOKEN }}  # optional
```

## Workflow Parameters

### Pre-release Workflow

| Parameter | Type | Default                                                | Description |
|-----------|------|--------------------------------------------------------|-------------|
| `python-version` | string | `'3.12'`                                               | Python version to use |
| `working-directory` | string | `'.'`                                                  | Working directory for the package |
| `skip-license-generation` | boolean | `false`                                                | Skip third-party license generation |
| `skip-header-check` | boolean | `false`                                                | Skip license header check/addition |
| `copyright-holder` | string | `'EcoFuture Technology Services LLC and contributors'` | Copyright holder name |
| `copyright-year` | string | `'2026'`                                               | Copyright year |

### Build & Publish Workflow

| Parameter | Type | Default  | Description |
|-----------|------|----------|-------------|
| `python-version` | string | `'3.12'` | Python version to use |
| `working-directory` | string | `'.'`    | Working directory for the package |
| `publish-to-testpypi` | boolean | `true`   | Publish to Test PyPI for pre-releases |
| `create-github-release` | boolean | `true`   | Create GitHub release |

### Required Secrets

| Secret | Required For | Description |
|--------|--------------|-------------|
| `PYPI_TOKEN` | Publish workflow | PyPI API token |
| `TEST_PYPI_TOKEN` | Publish workflow (optional) | Test PyPI API token for pre-releases |

## Setting up Secrets

1. Go to your repository settings
2. Navigate to `Secrets and variables` → `Actions`
3. Add repository secrets:
   - `PYPI_TOKEN`: Your PyPI API token from https://pypi.org/manage/account/token/
   - `TEST_PYPI_TOKEN` (optional): Your Test PyPI token from https://test.pypi.org/

## Tag Format

The build workflow expects tags in the following formats:
- Stable releases: `v1.0.0`, `v2.1.3`
- Pre-releases: `v1.0.0-rc1`, `v1.0.0-beta2`, `v1.0.0-alpha1`

## License

Apache License 2.0