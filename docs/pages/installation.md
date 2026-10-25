# Installation

## Requirements

- Python **3.9 or later**. CI runs on 3.9 to 3.13.
- [`pefile`](https://pypi.org/project/pefile/) and
  [`peid`](https://pypi.org/project/peid/) (2.4.0 or later), which pip
  installs automatically.
- *Optional:* `objdump` (from GNU binutils). It is only used to resolve long
  section names; see [Long section names](internals.md#long-section-names).

## From PyPI

```console
$ pip install pypackerdetect
```

This installs the `pypackerdetect` Python package and the `pypackerdetect`
console script.

## From source

```console
$ git clone https://github.com/packing-box/pypackerdetect
$ cd pypackerdetect
$ pip install .
```

For development, use an editable install:

```console
$ pip install -e .
```

## Checking the installation

```console
$ pypackerdetect --help
[...]
PyPackerDetect 1.2.1
[...]
```

```python
>>> from pypackerdetect.__info__ import __version__
>>> __version__
'1.2.1'
```

## Write access to the database folder

The first time a PEiD database is used, [peid](https://github.com/packing-box/peid)
writes a JSON cache of its search tree **next to the database file**. For the
embedded databases, that folder is the installed `pypackerdetect/db/` package
directory. If the folder is read-only, detection still works without the
cache, but each run rebuilds the search tree, which is slower.
