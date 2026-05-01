# Ruby Gem Build & Publish Action

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/n-at-han-k/ruby-gem-release-action)

A reusable composite GitHub Action to build a Ruby gem and publish it to GitHub Packages (GPR) and/or RubyGems.org.

## Inputs

| Input | Description | Required | Default |
|---|---|---|---|
| `ruby-version` | Ruby version to use | No | `3.3` |
| `publish-gpr` | Publish to GitHub Packages | No | `true` |
| `publish-rubygems` | Publish to RubyGems.org | No | `false` |
| `github-token` | GitHub token for GPR auth | No | |
| `rubygems-auth-token` | RubyGems.org API key | No | |
| `owner` | GitHub owner for GPR (defaults to repo owner) | No | |

## Usage Examples

### Build and publish to GPR on push to main

```yaml
name: Ruby Gem

on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]

jobs:
  build:
    name: Build + Publish
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - uses: actions/checkout@v4

      - name: Build and publish gem
        uses: n-at-han-k/ruby-gem-release-action@main
        with:
          publish-gpr: ${{ github.event_name == 'push' }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Build and publish to both GPR and RubyGems.org

```yaml
name: Ruby Gem

on:
  push:
    branches: ["main"]

jobs:
  build:
    name: Build + Publish
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - uses: actions/checkout@v4

      - name: Build and publish gem
        uses: n-at-han-k/ruby-gem-release-action@main
        with:
          publish-gpr: "true"
          publish-rubygems: "true"
          github-token: ${{ secrets.GITHUB_TOKEN }}
          rubygems-auth-token: ${{ secrets.RUBYGEMS_AUTH_TOKEN }}
```

### Build only (no publish) — e.g. for PRs

```yaml
name: Ruby Gem CI

on:
  pull_request:
    branches: ["main"]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build gem
        uses: n-at-han-k/ruby-gem-release-action@main
        with:
          publish-gpr: "false"
```

### Custom Ruby version

```yaml
      - name: Build and publish gem
        uses: n-at-han-k/ruby-gem-release-action@main
        with:
          ruby-version: "3.2"
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

## Notes

- The action expects a `.gemspec` file in the repository root.
- On PRs you typically want to set `publish-gpr: "false"` (or conditionally via `${{ github.event_name == 'push' }}`) to only build without publishing.
- The `packages: write` permission is required on the calling job when publishing to GPR.
- For RubyGems.org publishing, add your API key as a repository secret named `RUBYGEMS_AUTH_TOKEN`.
