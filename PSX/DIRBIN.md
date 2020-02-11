# Diablo 1 DIR/BIN File Format - Archives

## 1. Description

Diablo 1 PlayStation archives use the `.DIR` and `.BIN` file extensions (e.g. LUMP.DIR, LUMP.BIN).  
The `.DIR` file contains metadata and the `.BIN` file contains the actual data.

## 2. DIR file structure

```
{MAGIC NUMBER}
{FILE COUNT}
{FILE ENTRY METADATA} * {FILE COUNT}
```

`{MAGIC NUMBER}` is one DWORD with value `0x4C444952` (`LDIR` in ASCII).  
`{FILE COUNT}` is one little-endian DWORD representing the number of files in the archive.

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
The `.BIN` file stores file data in blocks of 2048 bytes.  
When the `.DIR` file contains a file with `{FILE SIZE}` value 0 then the `.BIN` file contains one 2048 bytes block filled with `0x00`.

### 3.1 `{FILE ENTRY}`

```
{SUM32 CHECKSUM}
{FILE DATA}
{BLANK SPACE}
```

`{SUM32 CHECKSUM}` is one little-endian DWORD containing the Sum32 checksum of the file.  
`{FILE DATA}` is the actual file data with a length of `{FILE SIZE}` plus some blank space filled with `0x00` up to the last block size (2048 bytes).
