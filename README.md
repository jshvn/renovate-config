# renovate-config

The Renovate policy shared by jshvn repositories. A repository's `renovate.json` extends it and
adds only what is its own:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>jshvn/renovate-config"]
}
```

## What it sets

- **Schedule.** Renovate proposes updates Monday before 08:00 Pacific and opens every one as a
  pull request.
- **Assignment.** Every pull request is assigned to jshvn. An assignment is a participating
  notification, so it arrives by email and lists at
  [github.com/pulls/assigned](https://github.com/pulls/assigned) without watching the
  repository. A pull request a repository sets to automerge is assigned only if its checks fail
  (Renovate's `assignAutomerge` default).
- **No caps.** `prHourlyLimit` and `prConcurrentLimit` are 0, so every due update is an open
  pull request.
- Everything else is `config:recommended`.

`default.json` is the preset; `renovate.json` is this repository's own config, which extends it
and keeps the action pin in the check current.

## Access

The repository is private. Mend's hosted Renovate app reads a private preset only from a
repository its installation includes, under the same account, so the installation must include
this repository and a consumer refers to it with the `local>` prefix. A repository under another
owner cannot extend it while it is private.

## Check

`.github/workflows/check.yml` runs Renovate's validator over both files. Locally, with Apple
`container` or Docker (same arguments):

```sh
container run --rm -v "$PWD":/work -w /work ghcr.io/renovatebot/renovate:latest \
  renovate-config-validator --strict --no-global default.json renovate.json
```

`--no-global` makes the validator read the files as repository config; without it, a named file
is read as self-hosted global config.
