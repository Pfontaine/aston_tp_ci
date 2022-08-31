# TP CI

## NF7-DO-01 et NF7-DO-01

Fait dans les paramètres du repo github [Pfontaine/aston_tp_ci](https://github.com/Pfontaine/aston_tp_ci)

## NF7-DO-03

```yaml
name: Check Conventionnal Commit

on:
  push:
    branches: 
      - 'feature/**'
      - 'hotfix/**'

jobs:
  check_commit:
    name: Check Commit
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: webiny/action-conventional-commits@v1.0.5
```

## NF8-DO-01

```yaml
name: Pull Request Workflow

on:
  pull_request:
    branches:
      - develop
    types:
      - opened

jobs:
  launch_test:
    if: startsWith(${{ github.head_ref }}, 'feature/')
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node_version: [14.x, 16.x]
      fail-fast: true
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node_version }}
      - run: npm ci
      - run: npm run build
      - run: npm run test
```

## NF8-DO-02

```yaml
name: Pull Request Workflow

on:
  pull_request:
    branches:
      - develop
    types:
      - opened

jobs:
  launch_test:
    if: startsWith(${{ github.head_ref }}, 'feature/')
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node_version: [14.x, 16.x]
      fail-fast: true
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node_version }}
      - run: npm ci
      - run: npm run build
      - run: npm run test
      - run: npm run test:coverage > coverage.txt
      - uses: actions/upload-artifact@v3
        with:
          name: test-coverage
          path: coverage.txt
```

## NF4-DO-01

```yaml
name: Issue assigned workflow

on:
  issues:
    types: [assigned]

jobs:
  create_branch:
    name: Create Branch
    runs-on: ubuntu-latest
    steps:
      - uses: robvanderleek/create-issue-branch@main
        name: Create Issue Branch
        env:
          GITHUB_TOKEN: ${{ secrets.WORKFLOW_TOKEN }}
```

## NF8-DO-04

```yaml
name: Auto Remove Merged Branch after Pull Request

on:
  pull_request:
    types: [closed]
    branches: [develop]

jobs:
  remove_branch:
    runs-on: ubuntu-latest
    name: Remove Branch
    if: github.event.pull_request.merged == true
    steps:
      - uses: koj-co/delete-merged-action@master
        name: Delete Branch
        with:
          branches: "!*, ${{ github.head_ref }}" 
        env:
          GITHUB_TOKEN: ${{ secrets.WORKFLOW_TOKEN }}
```
