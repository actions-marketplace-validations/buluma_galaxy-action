# Galaxy action

[![Yaml Lint](https://github.com/buluma/galaxy-action/actions/workflows/yamllint.yml/badge.svg)](https://github.com/buluma/galaxy-action/actions/workflows/yamllint.yml)

A GitHub action to publish your [Ansible](https://www.ansible.com/) role to [Galaxy](https://galaxy.ansible.com/).

## Requirements

This action expects the following (default Ansible role) structure:
```
.
├── defaults
│   └── main.yml
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── requirements.yml
├── tasks
│   └── main.yml
└── vars
    └── main.yml
```

## Inputs

### `galaxy_api_key`

The API Key for your personal Galaxy account. Found under https://galaxy.ansible.com/me/preferences . You can store this key in GitHub's Settings -> Secrets -> New repository secret. (Name: galaxy_api_key, Value: The token you copied from Galaxy.)

### `path`

For repositories that have multiple roles, you can specify a (relative) path to go into before releasing the role. Defaults to `./`. An example value could be `my_role`.

### `git_branch`

You may specify a specific branch to push. The default is `master`.

## Example usage

```yaml
---
name: GitHub Action

on:
  - push

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v3
      - name: galaxy
        uses: buluma/galaxy-action@v1.0.2
        with:
          galaxy_api_key: ${{ secrets.galaxy_api_key }}
```

Here is a another example that uses molecule to test the role and this Galaxy action to release:

```yaml
name: GitHub Action

on:
  - push

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v3
        with:
          path: "${{ github.repository }}"
      - name: molecule
        uses: buluma/molecule-action@v4.0.6
  release:
    needs:
      - test
    runs-on: ubuntu-latest
    steps:
      - name: galaxy
        uses: buluma/galaxy-action@v1.0.2
        with:
          galaxy_api_key: ${{ secrets.galaxy_api_key }}
```

When you have multiple roles in your repository, you can release one specific role by specifying a `path`:

```yaml
---
name: GitHub Action

on:
  - push

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v3
      - name: galaxy
        uses: buluma/galaxy-action@v1.0.2
        with:
          galaxy_api_key: ${{ secrets.galaxy_api_key }}
          path: my_role
          git_branch: my_branch
```
