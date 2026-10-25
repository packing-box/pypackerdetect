# PyPackerDetect

<p align="center"><img src="img/logo.png" alt="PyPackerDetect logo" width="200"></p>

**PyPackerDetect** detects whether a Windows PE executable is packed. It
combines several heuristics on the PE structure with
[PEiD](https://github.com/packing-box/peid) signatures. It is a complete
refactoring of [Cylance's PyPackerDetect](https://github.com/cylance/PyPackerDetect)
into a Python package with a console script.

[![PyPi](https://img.shields.io/pypi/v/pypackerdetect.svg)](https://pypi.python.org/pypi/pypackerdetect/)
[![Python Versions](https://img.shields.io/pypi/pyversions/pypackerdetect.svg)](https://pypi.python.org/pypi/pypackerdetect/)
[![Build Status](https://github.com/packing-box/pypackerdetect/actions/workflows/python-package.yml/badge.svg)](https://github.com/packing-box/pypackerdetect/actions/workflows/python-package.yml)
[![Security](https://github.com/packing-box/pypackerdetect/actions/workflows/security.yml/badge.svg)](https://github.com/packing-box/pypackerdetect/actions/workflows/security.yml)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.5732866.svg)](https://doi.org/10.5281/zenodo.5732866)
[![License](https://img.shields.io/pypi/l/pypackerdetect.svg)](https://pypi.python.org/pypi/pypackerdetect)

## Features

- **Six detection mechanisms** (see [How it works](internals.md)):
    - PEiD signatures;
    - known packer section names;
    - entry point in a non-standard section;
    - entry point in overlapping sections, or outside every section;
    - too many non-standard (or invalid) section names;
    - a low number of imports.
- **Two levels of findings**: *detections* are strong indicators of packing,
  *suspicions* are weaker hints.
- **Configurable**: each check can be turned off, thresholds can be tuned and
  the section-name and signature databases can be replaced with your own.
- **A command-line tool**, [`pypackerdetect`](cli/pypackerdetect.md), and a
  [Python API](api.md).

## Quick start

```console
$ pip install pypackerdetect
$ pypackerdetect calc_packed.exe
Packer report for: calc_packed.exe
	Detections: 3
	Suspicions: 0
	Log:
		[DETECTION] Entry point 0xc4f80 in irregular section(s): 'UPX1'
		[DETECTION] Section name 'UPX0' matches known packer: [UPX packer]
		[DETECTION] Section name 'UPX1' matches known packer: [UPX packer]
```

From Python:

```python
from pypackerdetect import PyPackerDetect

findings = PyPackerDetect().detect("program.exe")
print(findings["detections"], findings["suspicions"])
```

## Related projects

PyPackerDetect is part of the [packing-box](https://github.com/packing-box) ecosystem:

- [Awesome Executable Packing](https://github.com/packing-box/awesome-executable-packing): a curated list of resources about executable packing.
- [Bintropy](https://github.com/packing-box/bintropy): estimates how likely a binary is to contain compressed or encrypted bytes.
- [Dataset of packed ELF files](https://github.com/packing-box/dataset-packed-elf) and [Dataset of packed PE files](https://github.com/packing-box/dataset-packed-pe).
- [Docker Packing Box](https://github.com/packing-box/docker-packing-box): a Docker image of packers and tools for building datasets of packed executables.
- [DSFF](https://github.com/packing-box/python-dsff): the DataSet File Format library.
- [PEiD](https://github.com/packing-box/peid): a Python implementation of the Packed Executable iDentifier, used here for signature matching.
- [REMINDer](https://github.com/packing-box/reminder): a packing detector based on a simple entropy heuristic.

## License

PyPackerDetect is released under the
[GNU General Public License v3 or later](https://www.gnu.org/licenses/gpl-3.0.html).
The original project is © Cylance (author: Nick Cano).
