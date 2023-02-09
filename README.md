# Spell Check Lint Action

A linter for spell checking based on [check-spelling](https://github.com/check-spelling/check-spelling).

You may check [configuration documents here](https://github.com/check-spelling/check-spelling/wiki/Configuration) or the [action yaml](https://github.com/check-spelling/check-spelling/blob/main/action.yml) for default values and more.


## How to use in the working repository

* In the repository for which you want to spellcheck, add `.github/workflows/spellcheck.yml` with:
  ```yaml
  name: spellcheck

  on:
    pull_request:
      types: [opened, edited, reopened, synchronize]

  jobs:
    spelling:
      uses: Shypyard/spell-check-lint-action/.github/workflows/spellcheck.yml@main
      secrets: inherit
  ```

* [optional] Add some [config files](https://github.com/check-spelling/check-spelling/wiki/Configuration#files) (e.g. `excludes.txt` or `only.txt`) per repository preference.

* Commit, push, and create pull request as usual

* If there are typos or unrecognized words in the changed files, the spell checker job may fail, and you'll see comments about the unrecognized words in the pull request.

## How to test locally with the working repository

The recommended way to run locally is to use [act](https://github.com/nektos/act#). `act` is maintained by community so it may sometimes behave differently from the real GitHub runner.

1. Install `act`.

2. [optional] `act` may not support reusing workflow from a separate repository, so you may need to change the `spellcheck.yml` to be a completed workflow such as:
  ```yaml
  name: spellcheck

  on:
    pull_request:
      types: [opened, edited, reopened, synchronize]

  jobs:
    spelling:
      runs-on: ubuntu-latest
      name: Check Spelling
      permissions:
        contents: read
        pull-requests: read
        actions: read
      outputs:
        followup: ${{ steps.spelling.outputs.followup }}
      steps:
        - name: check-spelling
          id: spelling
          uses: check-spelling/check-spelling@main
          with:
            ...
            # paste from `./github/workflows/spellcheck.yml`
  ```

3. Run `act -j spelling`


## What to do when having a unrecognized word

* It's a typo

  Fix it.

* It's actually a valid word

  You can do either of the following:
  * Add the word to `Shypyard/spell-check-lint-action/dictionary.txt`
  * Add the word to `expect.txt` to the repository where the action is being triggered

* It's not even a word and shouldn't be checked

  * Exclude the file by adding it to `excludes.txt`
  * Add the pattern into `patterns.txt` so it'll be skipped
