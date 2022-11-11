# get-changed-files

[![CI status](https://github.com/elucidat/get-changed-files/workflows/Test/badge.svg)](https://github.com/elucidat/get-changed-files/actions?query=event%3Apush+branch%3Amain)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

Get all changed/modified files in a pull request (`pull_request` or `pull_request_target`) or push's commits.
You can choose to get all changed files, only added files, only modified files, only removed files, only renamed files, or all added and modified files.
These outputs are available via the `steps` output context.
The `steps` output context exposes the output names `all`, `added`, `modified`, `removed`, `renamed`, and `added_modified`.
Renamed files that are also modified are included in `renamed`, `modified` and `added_modified`.

This project is a fork of <https://github.com/Ana06/get-changed-files> (that in turn forks <https://github.com/jitterbit/get-changed-files>), which uses the latest core and github actions, sets the Node version to 16, and updates all the devDependencies.

- [Usage](#usage)
  - [Filtering](#filtering)
- [Examples](#examples)
  - [Get all changed files as space-delimited](#get-all-changed-files-as-space-delimited)
  - [Get all changed `*.php` files as space-delimited](#get-all-changed-php-files-as-space-delimited)
  - [Get all changed `*.yml` files but exclude `.github/*/*.yml` files](#get-all-changed-yml-files-but-exclude-githubyml-files)
  - [Get all added and modified files as CSV](#get-all-added-and-modified-files-as-csv)
  - [Get all removed files as JSON](#get-all-removed-files-as-json)
- [Install, Build, Lint, Test, and Package](#install-build-lint-test-and-package)
- [License](#license)

## Usage

See [action.yml](action.yml)

```yaml
- uses: elucidat/get-changed-files@v1.0.0
  with:
    # Format of the steps output context.
    # Can be 'space-delimited', 'csv', or 'json'.
    # Default: 'space-delimited'
    format: ''
    # Filter files using a glob filter
    filter: '*'
```

### Filtering

You can filter files using regular expressions with the `filter` option.
This option receives a list of patterns using [GitHub Actions syntax](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#filter-pattern-cheat-sheet).
You can use `!` at the start of a pattern to negate previous positive patterns.
See the [Get all changed `*.yml` files but exclude `.github/*/*.yml` files](#get-all-changed-yml-files-but-exclude-githubyml-files) example.

## Examples

### Get all changed files as space-delimited

If there are any files with spaces in them, then this method won't work and the step will fail.
Consider using one of the other formats if that's the case.

```yaml
- id: files
  uses: elucidat/get-changed-files@v1.0.0
- run: |
    for changed_file in ${{ steps.files.outputs.all }}; do
      echo "Do something with this ${changed_file}."
    done
```

### Get all changed `*.php` files as space-delimited

If there are any files with spaces in them, then this method won't work and the step will fail.
Consider using one of the other formats if that's the case.

```yaml
- id: files
  uses: elucidat/get-changed-files@v1.0.0
  with:
    filter: '*.php'
- run: |
    for changed_file in ${{ steps.files.outputs.all }}; do
      echo "Do something with this ${changed_file}."
    done
```

### Get all changed `*.yml` files but exclude `.github/*/*.yml` files

Be careful that the order of the glob has an importance.
Therefore, including all YML files first and excluding the YML files of your `.github/*/` directories is the way to go to exclude them.
If those two globs were inverted, you **would** include all the YML files, with the ones in your `.github/*/` directories.

```yaml
- uses: elucidat/get-changed-files@v1.0.0
  with:
    filter: |
      *.yml
      !.github/*/*.yml
```

### Get all added and modified files as CSV

```yaml
- id: files
  uses: elucidat/get-changed-files@v1.0.0
  with:
    format: 'csv'
    filter: '*'
- run: |
    mapfile -d ',' -t added_modified_files < <(printf '%s,' '${{ steps.files.outputs.added_modified }}')
    for added_modified_file in "${added_modified_files[@]}"; do
      echo "Do something with this ${added_modified_file}."
    done
```

### Get all removed files as JSON

```yaml
- id: files
  uses: elucidat/get-changed-files@v1.0.0
  with:
    format: 'json'
    filter: '*'
- run: |
    readarray -t removed_files <<<"$(jq -r '.[]' <<<'${{ steps.files.outputs.removed }}')"
    for removed_file in ${removed_files[@]}; do
      echo "Do something with this ${removed_file}."
    done
```

## Install, Build, Lint, Test, and Package

Make sure to do the following before checking in any code changes.

```bash
yarn
yarn all
```

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE)
