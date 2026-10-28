# Changelog

All notable changes to NDM Password Generator are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.1.1] - 2026-10-28

### Added

- `README.it.md`: the README in Italian.
- README: how to install and update, how to check that the script is the published
  one, the external services contacted (none), the project layout and the versioning.
- `CITATION.cff`: how to cite this software.

### Fixed

- After a clone the script can be run as `./ndm_password-generator.py`, as the README
  and `SECURITY.md` say: it was not executable in the repository.

## [1.1.0] - 2026-10-06

First public release.

### Added

- Passwords drawn with Python's `secrets` module (OS CSPRNG): no seed, no stored
  state, no network, no files.
- Alphanumeric by default, without the easily misread `I`, `O`, `l`, `0` and `1`
  (57-character alphabet); at least one upper case letter, lower case letter and digit.
- Output in groups (`-g`, `-w`, default 4 groups of 4) with a display-only
  separator (`-s`) on the `Readable` line.
- Exact entropy for the way the password is generated, with a strength label from
  `VERY WEAK` to `EXCELLENT`.
- `-l`, `--level`: the fewest whole groups that reach a requested strength.
- `-c`, `--charset`: `mixed`, `lower-digits`, `lower`, `digits`.
- `-y`, `--symbols` and `-Y`, `--symbol-set`: optional symbols (default `-_.`), never
  first or last, with their placement counted in the entropy.
- `-p`, `--prefix`: fixed text before the password, counted as no entropy.
- Warning for characters that need care in shell, config files or URLs.
- `-n`, `--count`, `-q`, `--quiet` (passwords only, for pipes), `-v`, `--verbose`
  (strength scale and security summary), `-V`, `--version`, `-h`, `--help` (with the
  security disclaimer).
- Requirements check (Python 3.8+, standard-library modules, OS random source);
  exit codes 0 (success), 2 (usage error), 3 (missing requirements).

[Unreleased]: https://github.com/nidamanx/NDM_Password-Generator/compare/v1.1.1...HEAD
[1.1.1]: https://github.com/nidamanx/NDM_Password-Generator/compare/v1.1.0...v1.1.1
[1.1.0]: https://github.com/nidamanx/NDM_Password-Generator/releases/tag/v1.1.0
