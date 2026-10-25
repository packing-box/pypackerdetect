# `pypackerdetect`: detect packing

`pypackerdetect` runs all the [detection mechanisms](../internals.md) on a PE
file and reports the findings, either as **detections** or **suspicions**.

```console
$ pypackerdetect [path arguments] [optional arguments] [threshold arguments] [-b] [-v] [-h] path
```

## Arguments

| Argument | Description |
|---|---|
| `path` | The PE file to analyze. It must exist. |

### Path arguments

| Option | Default | Description |
|---|---|---|
| `--packers-json JSON` | embedded | JSON file mapping known packer section names to packer descriptions (see [Databases](../internals.md#databases)). |
| `--peid-db-path TXT_DB` | embedded | PEiD signatures database to use instead of the embedded one. It takes precedence over `--peid-large-db`. |
| `--sections-json JSON` | embedded | JSON file with the lists of known section names. |

### Optional arguments

All checks are **enabled by default**. Each of these flags **turns one off**.

| Option | Turns off |
|---|---|
| `--bad-ep-sections` | The checks on the entry point section: irregular section, overlapping sections, no section, null entry point. |
| `--low-imports` | The check for a low number of imports. |
| `--packer-sections` | The check for known packer section names. |
| `--peid` | Detection with PEiD signatures. |
| `--peid-ep-only` | Entry point-only PEiD matching. PEiD then scans **every offset of the file** with the signatures that are not tied to the entry point, which is much slower. |
| `--unknown-sections` | The checks on the number of non-standard and invalid section names. |

| Option | Default | Description |
|---|---|---|
| `--peid-large-db` | off | Use the large embedded PEiD database (4,287 signatures) instead of the short one (1,832 signatures). |

!!! warning "Flags turn checks off"
    The option names describe the checks, not the action. For instance,
    `pypackerdetect program.exe --low-imports` **disables** the low imports
    check.

### Threshold arguments

| Option | Default | Description |
|---|---|---|
| `--bad-sections-threshold N` | 2 | Report a detection when at least `N` section names contain invalid characters. |
| `--imports-threshold N` | 10 | Report a detection when the executable has `N` imports or fewer. |
| `--unknown-sections-threshold N` | 3 | Report a detection when at least `N` section names are non-standard. |

`N` must be a non-negative integer.

### Extra arguments

| Option | Description |
|---|---|
| `-b`, `--benchmark` | Print the elapsed time, in seconds, after the report. |
| `-v`, `--verbose` | Show debug information, including each finding as it is found. |
| `-h`, `--help` | Show the help message and exit. |

## Output

```console
$ pypackerdetect calc_packed.exe
Packer report for: calc_packed.exe
	Detections: 3
	Suspicions: 0
	Log:
		[DETECTION] Entry point 0xc4f80 in irregular section(s): 'UPX1'
		[DETECTION] Section name 'UPX0' matches known packer: [UPX packer]
		[DETECTION] Section name 'UPX1' matches known packer: [UPX packer]
```

The `Log:` section only appears when there is at least one finding. See
[Findings](../internals.md#findings) for the list of messages.

## Exit code

| Code | Meaning |
|---|---|
| `0` | The analysis ran, whatever the findings. |
| `1` | The analysis failed, for instance because the input is not a valid PE file. |
| `2` | Invalid arguments. |

!!! note
    The exit code does not tell whether the file is packed. Parse the report,
    or use the [Python API](../api.md), for that.

## Examples

```console
$ pypackerdetect program.exe
$ pypackerdetect program.exe -b
$ pypackerdetect program.exe --low-imports --unknown-sections   # disable these two checks
$ pypackerdetect program.exe --imports-threshold 5 --bad-sections-threshold 5
$ pypackerdetect program.exe --peid-db-path user_db.txt --sections-json sections.json
```
