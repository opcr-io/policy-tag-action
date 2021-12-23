# Policy Tag Action

## policy-tag-action

Create tag for target image that refers to source image.

## Inputs

### `source_tag`

**Required** The source image tag.

Default: empty

### `target_tags`

**Required** One or more target image tags.

Default: empty

### `verbosity`

**Required** The logging verbosity level [ `info` | `error` | `debug` | `trace` ] used.

Default: `error`


## Outputs

None defined


## Example

```
name: policy-build-release

on:
  workflow_dispatch:
  push:
    tags:
    - '*'

jobs:
  release_policy:
    runs-on: ubuntu-latest
    name: build
    steps:

    - uses: actions/checkout@v2

    - name: Policy Login
      id: policy-login
      uses: opcr-io/policy-login-action@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        username: policy-bot
        password: "$GITHUB_TOKEN"

    - name: Policy Build
      id: policy-build
      uses: opcr-io/policy-build-action@v2
      with:
        src: peoplefinder/src
        tag: datadude/peoplefinder
        revision: "$GITHUB_SHA"

    - name: Policy Tag
      id: policy-tag
      uses: opcr-io/policy-build-action@v2
      with:
        source_tag: datadude/peoplefinder
        input_tags: datadude/peoplefinder:$(sver -n patch)

    - name: Policy Push
      id: policy-push
      uses: opcr-io/policy-push-action@v2
      with:
        tags: datadude/peoplefinder:$(sver -n patch)

    - name: Policy Logout
      id: policy-logout
      uses: opcr-io/policy-logout-action@v2

```
