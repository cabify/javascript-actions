# javascript-actions

Repository to share Javascript themed Github Actions

## Actions available

### Tests

This workflow will perform all the steps dedicated to test any suite. This step requires a few `yarn` scripts to exists:

- `yarn lint`
- `yarn format:check`
- `yarn typecheck`
- `yarn test:ci`

Below you will find an example on how `package.json/scripts` object may looks like:

```json
    "format:check": "prettier --check .",
    "lint": "eslint . --ext ts,tsx,js",
    "test:ci": "jest",
    "typecheck": "tsc --project tsconfig.eslint.json"
```

Below you can find what your `.github/workflows/tests.yml` may looks like:

```yml
name: Tests

on: [push]

jobs:
  test:
    uses: cabify/javascript-actions/.github/workflows/tests.yml@main
```

It means this this workflow will run on every push.


### NPM Publish

This workflow will allow your library to be published in NPM public registry. In order for you to publish a library you may want to build the package before publish, you can always do this by putting the following scripts in `package.json/scripts`:

```json
    "clean": "rimraf dist/* es2015/*",
    "build": "yarn run clean && cross-env BABEL_ENV=build package-build",
    "prepublishOnly": "yarn run build",
```

The workflow will receive the following inputs:

- inputs: tag (containing the tag NPM will use to tag the version, it may be `latest` or anything else, [more information](https://docs.npmjs.com/cli/v8/commands/npm-dist-tag)).
- secrets: token (NPM registry token with `publish` privileges, ask anybody in the Cabify's Frontend Organization about it).

Below you can find what your `.github/workflows/npm-publish.yml` may looks like:

```yml
name: Publish package

on:
  release:
    types: [created]

jobs:
  publish-npm:
    uses: cabify/javascript-actions/.github/workflows/npm_publish.yml@main
    with:
      # This means that every version tagged with `beta` will be published as a `beta` tagged version
      tag: ${{ contains(github.ref_name,'beta') && 'beta' || 'latest' }} 
    secrets:
      token: ${{ secrets.NPM_TOKEN }}
```
