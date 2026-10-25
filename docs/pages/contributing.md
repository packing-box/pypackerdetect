# Contributing

Contributions are welcome, especially **new packer section names** and
**false positive reports**.

## Reporting issues

Open an issue on [GitHub](https://github.com/packing-box/pypackerdetect/issues)
and include:

- the PyPackerDetect version (`pypackerdetect --help` prints it);
- the exact command and its output, ideally with `-v`;
- if possible, a link to (or the hash of) the sample.

## Security

Please do **not** report vulnerabilities in public issues. See the
[security policy](https://github.com/packing-box/pypackerdetect/blob/main/SECURITY.md):
report them privately through the repository's *Security* tab
(*Report a vulnerability*).

Dependencies are monitored by Dependabot and audited with
[pip-audit](https://github.com/pypa/pip-audit) by the `security` workflow
(`.github/workflows/security.yml`), on every push and pull request to `main`
and weekly.

## Adding section names

- **Packer section names** go in `src/pypackerdetect/db/packers.json`, with a
  short description of the packer. Avoid names that are also standard
  section names: they are ignored anyway (see [Section names](internals.md#section-names)).
- **Standard section names** that cause false positives go in the `known` list
  of `src/pypackerdetect/db/sections.json`.

Test your change on packed and unpacked samples. The
[packed PE dataset](https://github.com/packing-box/dataset-packed-pe) and
[Docker Packing Box](https://github.com/packing-box/docker-packing-box) help
with this. Section names are compared after stripping null bytes and spaces,
so do not add trailing spaces to the names.

## Development setup

```console
$ git clone https://github.com/packing-box/pypackerdetect
$ cd pypackerdetect
$ pip install -e .
$ pip install flake8
$ flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
```

CI (`.github/workflows/python-package.yml`) installs the package on Python
3.9 to 3.13 and runs this flake8 check.

## Releasing

The version is stored in `src/pypackerdetect/VERSION.txt`. On each push to
`main`, CI checks whether that version is already on PyPI. If it is not, CI
builds the package and publishes it.

## Building this documentation

```console
$ pip install -r docs/requirements.txt
$ mkdocs serve -f docs/mkdocs.yml          # live preview at http://127.0.0.1:8000
$ mkdocs build --strict -f docs/mkdocs.yml # what Read the Docs runs
```

Read the Docs builds the site from `.readthedocs.yaml` at the root of the
repository.
