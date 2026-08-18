# companion-module-generic-filereader — ACA production build

See [HELP.md](./companion/HELP.md) and [LICENSE](./LICENSE)

This is the Atheist Community of Austin's build of the Generic File Reader module.
It is upstream `bitfocus/companion-module-generic-filereader` plus two changes that
our live show system depends on:

1. **Companion variables in the configured file path.** Lets one connection follow a
   path that changes at runtime — our show-picker button repoints the readers at the
   current show's text directory. Upstream treats `$(custom:x)/f.txt` as a literal
   filename. Offered upstream as
   [PR #35](https://github.com/bitfocus/companion-module-generic-filereader/pull/35),
   which closes upstream issue #14.
2. **Polling recovery.** Upstream stops polling permanently when the file goes
   missing, never retries if the file is absent at startup, and reports the
   connection as healthy even when it has failed. Offered upstream as
   [PR #36](https://github.com/bitfocus/companion-module-generic-filereader/pull/36).

Each change is also on its own branch (`feature/variables-in-file-path`,
`fix/polling-recovery`), rebased on upstream `main`, which is what the PRs point at.
This `aca/production` branch is the combined build we actually run.

## Why this exists

If this module is ever reinstalled or updated from the Companion module store, both
changes are lost, and the failure is quiet: captions and lower-third names simply stop
following the show picker while the connections still show green. The six File Reader
connections on the production machine are therefore set to update policy **Manual** so
Companion never offers to replace this build.

If upstream merges the PRs above and releases a version containing them, switching to
the official release is the right move — this fork stops being necessary.

## Building an installable package

Push a tag beginning with `v` and GitHub Actions builds the module package and attaches
it to a release:

```
git tag v2.2.2-beta.2
git push origin v2.2.2-beta.2
```

Download the `generic-filereader-<version>.tgz` from the
[Releases](https://github.com/ACA-Tech/companion-module-generic-filereader/releases)
page. In Companion, open **Modules**, use *import module package*, then select that
version on each File Reader connection.

Keep the version string matching what is installed in production so the rebuilt package
is a drop-in replacement.

## Version History

### 2.2.2-beta.2 (2026-07-21)
* Fix: Continue polling after a configured file path becomes unavailable
* Fix: Automatically restore the connection status when the file becomes available again
* Fix: Replace the existing polling timer when file reading is restarted

### 2.2.2-beta.1 (2026-07-16)
* Feature: Allow Companion variables in the configured file path
* Fix: Resolve variables in the configured path when reading a specific line

### 2.2.1 (2025-06-26)
* Bugfix: Read File Now action was not updating the module variables
* Chore: Bump Companion-base to 1.4.3
