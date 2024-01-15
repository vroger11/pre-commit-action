This action is a fork of pre-commit/action. It is designed to add some new features of pre-commit.ci and maintain the action. 
Generally you want to use [pre-commit.ci] which is faster and has more features.

[pre-commit.ci]: https://pre-commit.ci

___

[![pre-commit.ci status](https://results.pre-commit.ci/badge/github/pre-commit/action/main.svg)](https://results.pre-commit.ci/latest/github/pre-commit/action/main)
[![Build Status](https://github.com/pre-commit/action/actions/workflows/main.yml/badge.svg)](https://github.com/pre-commit/action/actions)

pre-commit/action
=================

a GitHub action to run [pre-commit](https://pre-commit.com)

### using this action

To use this action, make a file `.github/workflows/pre-commit.yml`.  Here's a
template to get started:

```yaml
name: pre-commit

on:
  pull_request:
  push:
    branches: [main]

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: actions/setup-python@v3
    - uses: vroger11/pre-commit/action@v3.1.0
```

This does a few things:

- clones the code
- installs python
- sets up the `pre-commit` cache

### using this action with custom invocations

By default, this action runs all the hooks against all the files.  `extra_args`
lets users specify a single hook id and/or options to pass to `pre-commit run`.

Here's a sample step configuration that only runs the `flake8` hook against all
the files (use the template above except for the `pre-commit` action):

```yaml
    - uses: vroger11/pre-commit/action@v3.1.0
      with:
        extra_args: flake8 --all-files
```

If you want to skip multiple hooks you can use `skip` that works like `SKIP` 
environment variable for pre-commit.

Here's an example:
```yaml
    - uses: vroger11/pre-commit/action@v3.1.0
      with:
        skip: hook1,hook2,hook3
```

### using this action in private repositories

prior to v3.0.0, this action had custom behaviour which pushed changes back to
the pull request when supplied with a `token`.

this behaviour was removed:
- it required a PAT (didn't work with short-lived `GITHUB_TOKEN`)
- properly hiding this `input` from the installation and execution of hooks
  is intractable in github actions (it is readily available as `$INPUT_TOKEN`)
- this meant potentially unvetted code could access the token via the
  environment

you can _likely_ achieve the same thing with an external action such as
[git-auto-commit-action] though you may want to take precautions to clear `git`
hooks or other ways that arbitrary code execution can occur when running
`git commit` / `git push` (for example [core.fsmonitor]).

while unrelated to this action, [pre-commit.ci] avoids these problems by
installing and executing isolated from the short-lived repository-scoped
[installation access token].

[git-auto-commit-action]: https://github.com/stefanzweifel/git-auto-commit-action
[core.fsmonitor]: https://github.blog/2022-04-12-git-security-vulnerability-announced/
[pre-commit.ci]: https://pre-commit.ci
[installation access token]: https://docs.github.com/en/rest/apps/apps#create-an-installation-access-token-for-an-app
