# workflows

This repo contains a collection of reusable GitHub Actions workflows.

# Contents

- [workflows](#workflows)
- [Contents](#contents)
- [Contributing](#contributing)
- [IaC Checks](#iac-checks)
  - [Inputs](#inputs)
  - [Usage](#usage)

# Contributing

- Follow [conventional commits v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) for commits _and_ PR titles.
- Follow [GitHub Flow](https://githubflow.github.io) for branching strategy and PR process.

# IaC Checks

The [IaC Checks workflow](./iac-checks.yaml) runs a basic set of checks on Terraform or OpenTofu codebases.

Checks include:

1. [Verifying format](https://opentofu.org/docs/cli/commands/fmt/)
2. [Validating the IaC config](https://opentofu.org/docs/cli/commands/validate/)
3. Running [tflint](https://github.com/terraform-linters/tflint)
4. Running [checkov](https://www.checkov.io)

## Inputs

| Name              | Description                             | Required | Default |
| ----------------- | --------------------------------------- | -------- | ------- |
| tool              | Tool to use (`terraform` or `tofu`)     | true     |         |
| tool-version      | Version of Terraform or OpenTofu to use | false    | latest  |
| working-directory | Location of IaC configuration           | false    | .       |

> [!NOTE]
> The `tool-version` can be a specific version, or a [SemVer range](https://www.npmjs.com/package/semver#ranges)

## Usage

Set up a new workflow in your IaC repository, referencing this reusable workflow and passing in the required inputs.

An example using only a single working directory:

```yaml
name: IaC Checks

on:
  pull_request:
    paths: ["**/.tf"]

jobs:
  iac-checks:
    name: IaC Checks
    uses: ehassett/workflows/.github/workflows/iac-checks.yaml@main
    with:
      tool: "tofu"
      tool-version: "1.9.0"
      working-directory: "infrastructure"
```

An example using multiple subdirectories:

```yaml
name: IaC Checks

on:
  pull_request:
    paths: ["**/.tf"]

jobs:
  iac-checks:
    name: IaC Checks
    uses: ehassett/workflows/.github/workflows/iac-checks.yaml@main
    strategy:
      fail-fast: false
      matrix:
        subdirectory: ["aws", "gcp"]
    with:
      tool: "tofu"
      tool-version: "1.9.0"
      working-directory: "infrastructure/${{ matrix.subdirectory }}"
```
