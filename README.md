# renovate-config

The Renovate policy shared by jshvn repositories. A repository's `renovate.json` extends it and
adds only what is its own, such as a custom manager for a pin Renovate cannot read on its own, or
a rule for a package whose every release is reviewed one at a time:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>jshvn/renovate-config"]
}
```

`local>jshvn/renovate-config` names the same preset from a repository on github.com.

## What it sets

- **Renovate's best practices.** `config:best-practices`: container images pinned to a digest,
  GitHub Actions pinned to a commit, devDependencies pinned, lock files refreshed weekly,
  configuration migrated when Renovate renames an option, packages a year without a release
  flagged as abandoned.
- **One pull request a week.** Every non-major update in a repository lands in one pull request,
  "all non-major dependencies"; each major is its own. New pull requests open on Monday before
  08:00 Pacific, lock file maintenance in the same window. A security fix opens as soon as
  Renovate learns of it, whatever the day.
- **Three days old.** A release is proposed once it is three days old, so a registry has time to
  pull a bad one. Where a registry publishes no release time (lscr.io and ghcr.io images, git
  revisions), the update is proposed at once.
- **Assignment.** Every pull request is assigned to jshvn. An assignment is a participating
  notification, so it arrives by email and lists at
  [github.com/pulls/assigned](https://github.com/pulls/assigned) without watching the
  repository. A pull request a repository sets to automerge is assigned only if its checks fail
  (Renovate's `assignAutomerge` default).
- **No caps.** `prHourlyLimit` and `prConcurrentLimit` are 0, so every due update is an open
  pull request and nothing waits on the Dependency Dashboard as rate-limited.

`default.json` is the preset; `renovate.json` is this repository's own config, which extends it
and keeps the action pin in the check current.

## Onboarding

When the Mend Renovate app is installed on a jshvn repository that has no Renovate config, its
onboarding pull request extends this preset: Renovate looks for a repository named
`renovate-config` with a `default.json` under the same account. Merging that pull request is the
whole setup for a repository with nothing of its own to add.

## Check

`.github/workflows/check.yml` runs Renovate's validator over both files. Locally, with Apple
`container` or Docker (same arguments):

```sh
container run --rm -v "$PWD":/work -w /work ghcr.io/renovatebot/renovate:latest \
  renovate-config-validator --strict --no-global default.json renovate.json
```

`--no-global` makes the validator read the files as repository config; without it, a named file
is read as self-hosted global config.
