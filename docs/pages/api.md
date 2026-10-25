# Python API

The `pypackerdetect` package exposes one class:

```python
from pypackerdetect import PyPackerDetect
```

---

## `PyPackerDetect`

```python
PyPackerDetect(peid=True, peid_large_db=False, peid_ep_only=True, peid_db_path=None,
               bad_ep_sections=True, low_imports=True, packer_sections=True,
               unknown_sections=True, import_threshold=10, unknown_sections_threshold=3,
               bad_sections_threshold=2, logger=None, packers_json=None,
               sections_json=None, **kwargs)
```

Configures a detector. The JSON databases are loaded once, here, so one
instance can analyze many files.

| Parameter | Type | Description |
|---|---|---|
| `peid` | `bool` | Match PEiD signatures. |
| `peid_large_db` | `bool` | Use the large embedded PEiD database instead of the short one. |
| `peid_ep_only` | `bool` | Match PEiD signatures at the entry point only. `False` scans the whole file. |
| `peid_db_path` | `str` or `None` | Path to a custom PEiD database. It takes precedence over `peid_large_db`. |
| `bad_ep_sections` | `bool` | Check the section(s) holding the entry point. |
| `low_imports` | `bool` | Check the number of imports. |
| `packer_sections` | `bool` | Check for known packer section names. |
| `unknown_sections` | `bool` | Check the number of non-standard and invalid section names. |
| `import_threshold` | `int` | Report a detection when there are this many imports or fewer. |
| `unknown_sections_threshold` | `int` | Report a detection from this many non-standard section names. |
| `bad_sections_threshold` | `int` | Report a detection from this many section names with invalid characters. |
| `logger` | `logging.Logger` | Optional logger for debug output. |
| `packers_json` | `str` or `None` | Path to a custom packer section names JSON. `None` uses the embedded one. |
| `sections_json` | `str` or `None` | Path to a custom known section names JSON. `None` uses the embedded one. |
| `**kwargs` | | Ignored. It lets you pass `vars(args)` from an argument parser. |

See [Databases](internals.md#databases) for the JSON formats.

### `detect`

```python
PyPackerDetect.detect(pe)
```

Analyzes one executable.

| Parameter | Type | Description |
|---|---|---|
| `pe` | `str` or `pefile.PE` | Path to the executable, or an already parsed `pefile.PE` object. |

**Returns** a dictionary with two lists of messages:

```python
{'detections': [...], 'suspicions': [...]}
```

See [Findings](internals.md#findings) for the possible messages.

**Raises** `pefile.PEFormatError` if the file is not a valid PE file.

!!! note "Passing a `pefile.PE` object"
    PEiD and `objdump` work on files. If the `PE` object has a `path`
    attribute, that file is used. Otherwise (for instance with
    `PE(data=...)`), the data is copied to a temporary file for the duration
    of the analysis.

### `report`

```python
PyPackerDetect.report(pe, findings)   # static method
```

Prints the findings returned by [`detect`](#detect) in the format of the
original project (see [Output](cli/pypackerdetect.md#output)). `pe` is the
path or the `pefile.PE` object, and is only used for the report header.

## Example

```python
import logging
from pefile import PE
from pypackerdetect import PyPackerDetect

detector = PyPackerDetect(peid_large_db=True, import_threshold=5,
                          logger=logging.getLogger("pypackerdetect"))

for path in ["a.exe", "b.exe"]:
    findings = detector.detect(path)
    if findings["detections"]:
        print(f"{path} looks packed")
    PyPackerDetect.report(path, findings)

# with a PE object parsed from memory
with open("c.exe", "rb") as f:
    findings = detector.detect(PE(data=f.read()))
```
