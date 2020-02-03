# Diablo 1 PlayStation Archive File Format

## 1. Description

Diablo 1 PlayStation archives use the `.DIR` and `.BIN` file extensions (e.g. LUMP.DIR, LUMP.BIN).  
The `.DIR` file contains metadata and the `.BIN` file contains the actual data.

## 2. DIR file structure

```
{MAGIC NUMBER}
{BLOCK SIZE}?
{FILE ENTRY METADATA} * {NUMBER OF FILES}
```

TODO: Magic number, block size


### 2.1 `{FILE ENTRY METADATA}`

```
{BIN OFFSET}
{FILE SIZE}
{FILE NAME}
```

`{BIN OFFSET}` is one little-endian DWORD referring to the offset of the file in the associated `.BIN` file.  
`{FILE SIZE}` is one little-endian DWORD representing the file size.  
`{FILE NAME}` is a three DWORDs long ASCII string containing the file name.

## 3. BIN file structure

```
{FILE ENTRY} * {NUMBER OF FILES}
```
The `.BIN` file stores file data in blocks of `{BLOCK SIZE}` (cf. 2.).

### 3.1 `{FILE ENTRY}`

```
{SUM32 CHECKSUM}
{FILE DATA}
{BLANK SPACE}
```

`{SUM32 CHECKSUM}` is one little-endian DWORD containing the Sum32 checksum of the file.  
`{FILE DATA}` is the actual file data with a length of `{FILE SIZE}` plus some blank space filled with `0x00` up to the `{BLOCK SIZE}` (cf. 2.).
