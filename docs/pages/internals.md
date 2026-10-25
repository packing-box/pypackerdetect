# How it works

## Overview

```text
                      ┌── section names ──▶ known/unknown/packer/invalid names
 program.exe ─pefile─▶├── entry point ────▶ section(s) holding it
                      ├── import table ───▶ number of imports
                      └── file ──── peid ─▶ PEiD signatures
                                              │
                                              ▼
                              {'detections': [...], 'suspicions': [...]}
```

[pefile](https://github.com/erocarrera/pefile) parses the executable, then
each enabled check adds its findings. A **detection** is a strong indicator
of packing. A **suspicion** is a weaker hint.

## Section names

Each section name is decoded as ASCII, then stripped of padding (null bytes
and spaces). A name that is not valid ASCII is an **invalid name**: it
produces a suspicion, and it is counted for the
[bad sections threshold](cli/pypackerdetect.md#threshold-arguments).

A valid name is then:

- **unknown** if it is not in the `known` list of `sections.json`;
- a **packer section** if it is a key of `packers.json` and is not a known
  name, so that a packer entry cannot clash with a standard name.

### Long section names

Section names are limited to 8 bytes. Some compilers (for instance MinGW with
debug information) write longer names in the COFF string table and store
`/N` in the section header, where `N` is the offset of the name in that
table. For such names, PyPackerDetect runs `objdump -h` on the file and uses
the real names. If `objdump` is not installed, fails or times out (after 10
seconds), the `/N` name is kept.

## Entry point section

The entry point is the `AddressOfEntryPoint` field of the optional header. A
section holds it when:

```text
VirtualAddress <= entry point < VirtualAddress + VirtualSize
```

`SizeOfRawData` is used instead of `VirtualSize` when the latter is zero, as
the Windows loader does.

Then:

1. A null entry point is a suspicion.
2. If no section holds the entry point, it is a detection.
3. If several sections hold it, they overlap, which is a detection.
4. If none of the entry point sections is `acceptable` (`.text`), the
   section is irregular, unless one of these exceptions applies:
    - the file has a Delphi BSS section (`delphi-bss`) and the entry point is
      in a Delphi code section (`delphi`: `.itext`, `CODE`);
    - the file has no `.text` section at all and the entry point is in an
      `alternative` section (`.code`, `text`, `.text0`, ...);
    - the file has a `.text` section and the entry point is in a `driver`
      section (`INIT`).

## Imports

The imports of all the DLLs in the import table are counted. A count lower
than or equal to the threshold (10 by default) is a detection, as packers
usually only import what they need to unpack the payload (often
`LoadLibrary` and `GetProcAddress`). A file with no import table counts as 0
imports.

.NET assemblies (importing `mscoree.dll`) are skipped, as they always have
very few native imports.

## PEiD signatures

The file is passed to
[`peid.identify_packer`](https://peid.readthedocs.io/en/latest/api.html#identify_packer)
with the chosen database. By default, only the signatures that apply at the
entry point are matched, using the short embedded database. Every match is
reported in one detection.

## Findings

| Level | Message | Check |
|---|---|---|
| Suspicion | `Section name with invalid characters` | `unknown_sections` (always collected) |
| Suspicion | `Null entry point` | `bad_ep_sections` |
| Detection | `Entry point 0x… does not fall in any section` | `bad_ep_sections` |
| Detection | `Entry point 0x… falls in overlapping sections: '…'` | `bad_ep_sections` |
| Detection | `Entry point 0x… in irregular section(s): '…'` | `bad_ep_sections` |
| Detection | `Too few imports (total: N)` | `low_imports` |
| Detection | `Detected N non-standard sections: '…'` | `unknown_sections` |
| Detection | `Detected N sections with invalid names` | `unknown_sections` |
| Detection | `Section name '…' matches known packer: […]` | `packer_sections` |
| Detection | `Found PEID signature: …` | `peid` |

## Databases

The embedded databases are in the `pypackerdetect/db/` package folder.

### `packers.json`

Known packer section names, mostly from
[Hexacorn](https://www.hexacorn.com/blog/2016/12/15/pe-section-names-re-visited/).
The `packers` key maps each section name to a description:

```json
{
    "source": "https://www.hexacorn.com/blog/2016/12/15/pe-section-names-re-visited/",
    "last_update": "Jul 11 2021",
    "packers": {
        "UPX0": "UPX packer",
        ".MPRESS1": "Mpress Packer"
    }
}
```

### `sections.json`

Lists of standard section names, under the `sections` key:

| Key | Use |
|---|---|
| `known` | Standard section names; any other name is non-standard. |
| `acceptable` | Regular entry point sections. |
| `alternative` | Entry point sections accepted when there is no `.text` section. |
| `delphi-bss` | BSS sections that identify a Delphi executable. |
| `delphi` | Entry point sections accepted for Delphi executables. |
| `driver` | Entry point sections accepted for drivers. |

A custom file passed with `--sections-json` must contain all of these keys.

### PEiD databases

`sigs_short.txt` (1,832 signatures, the default) and `sigs_long.txt` (4,287
signatures, `--peid-large-db`) are in the PEiD text format, described in the
[PEiD documentation](https://peid.readthedocs.io/en/latest/signatures.html).
