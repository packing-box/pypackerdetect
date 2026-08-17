<p align="center"><img src="https://github.com/packing-box/PyPackerDetect/raw/main/docs/logo.png"></p>
<h1 align="center">PyPackerDetect <a href="https://twitter.com/intent/tweet?text=PyPackerDetect%20-%20Packer%20detector%20using%20multiple%20heuristics%20and%20PEiD's%20signatures%20database.%0D%0Ahttps%3a%2f%2fgithub%2ecom%2fpacking-box%2fPyPackerDetect%0D%0A&hashtags=python,pe,peid,packer,packingdetection"><img src="https://img.shields.io/badge/Tweet--lightgrey?logo=twitter&style=social" alt="Tweet" height="20"/></a></h1>
<h3 align="center">Detect packers on PE files using heuristics and signatures.</h3>

[![PyPi](https://img.shields.io/pypi/v/pypackerdetect.svg)](https://pypi.python.org/pypi/pypackerdetect/)
[![Python Versions](https://img.shields.io/pypi/pyversions/pypackerdetect.svg)](https://pypi.python.org/pypi/pypackerdetect/)
[![Build Status](https://github.com/packing-box/pypackerdetect/actions/workflows/python-package.yml/badge.svg)](https://github.com/packing-box/pypackerdetect/actions/workflows/python-package.yml)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.5732866.svg)](https://doi.org/10.5281/zenodo.5732866)
[![License](https://img.shields.io/pypi/l/pypackerdetect.svg)](https://pypi.python.org/pypi/pypackerdetect)


A complete refactoring of [this project](https://github.com/cylance/PyPackerDetect) to a Python package with a console script to detect whether an executable is packed.

[pefile](https://github.com/erocarrera/pefile) is used for PE parsing. [peid](https://github.com/packing-box/peid) is used as implementation of PEiD.

```session
$ pip install pypackerdetect
```

```session
$ pypackerdetect --help
[...]
usage examples:
- pypackerdetect program.exe
- pypackerdetect program.exe -b
- pypackerdetect program.exe --low-imports --unknown-sections
- pypackerdetect program.exe --imports-threshold 5 --bad-sections-threshold 5
```

## :bulb: Detection Mechanisms

- PEID signatures
- Known packer section names
- Entrypoint in non-standard section
- Threshhold of non-standard sections reached
- Low number of imports
- Overlapping entrypoint sections


## :star: Related Projects

You may also like these:

- [Awesome Executable Packing](https://github.com/packing-box/awesome-executable-packing): A curated list of awesome resources related to executable packing.
- [Bintropy](https://github.com/packing-box/bintropy): Analysis tool for estimating the likelihood that a binary contains compressed or encrypted bytes (inspired from [this paper](https://ieeexplore.ieee.org/document/4140989)).
- [Dataset of packed ELF files](https://github.com/packing-box/dataset-packed-elf): Dataset of ELF samples packed with many different packers.
- [Dataset of packed PE files](https://github.com/packing-box/dataset-packed-pe): Dataset of PE samples packed with many different packers (fork of [this repository](https://github.com/chesvectain/PackingData)).
- [Docker Packing Box](https://github.com/packing-box/docker-packing-box): Docker image gathering packers and tools for making datasets of packed executables.
- [DSFF](https://github.com/packing-box/python-dsff): Library implementing the DataSet File Format (DSFF).
- [PEiD](https://github.com/packing-box/peid): Python implementation of the well-known Packed Executable iDentifier ([PEiD](https://www.aldeid.com/wiki/PEiD)).
- [REMINDer](https://github.com/packing-box/reminder): Packing detector using a simple heuristic (inspired from [this paper](https://ieeexplore.ieee.org/document/5404211)).


## :clap:  Supporters

[![Stargazers repo roster for @packing-box/PyPackerDetect](https://reporoster.com/stars/dark/packing-box/PyPackerDetect)](https://github.com/packing-box/PyPackerDetect/stargazers)

[![Forkers repo roster for @packing-box/PyPackerDetect](https://reporoster.com/forks/dark/packing-box/PyPackerDetect)](https://github.com/packing-box/PyPackerDetect/network/members)

<p align="center"><a href="#"><img src="https://img.shields.io/badge/Back%20to%20top--lightgrey?style=social" alt="Back to top" height="20"/></a></p>
