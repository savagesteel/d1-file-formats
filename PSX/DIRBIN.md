# Diablo 1 DIR/BIN File Format - Archives

## 1. Description

Diablo 1 PlayStation archives use the `.DIR` and `.BIN` file extensions (e.g. LUMP.DIR, LUMP.BIN).  
The `.DIR` file contains metadata and the `.BIN` file contains the actual data.

DIR/BIN Data is stored using little-endian byte order.


## 2. DIR file structure

```
{MAGIC NUMBER}
{FILE COUNT}
{FILE ENTRY METADATA} * {FILE COUNT}
```

`{MAGIC NUMBER}` is one DWORD with value `0x4C444952` (`LDIR` in ASCII).  
`{FILE COUNT}` is one DWORD representing the number of files in the archive.


### 2.1 `{FILE ENTRY METADATA}`

```
{BIN OFFSET}
{FILE SIZE}
{FILE NAME}
```

`{BIN OFFSET}` is one DWORD referring to the offset of the file in the associated `.BIN` file.  
`{FILE SIZE}` is one DWORD representing the file size.  
`{FILE NAME}` is a three DWORDs long ASCII string containing the file name.


## 3. BIN file structure

```
{ {FILE ENTRY} | {VAG FILE ENTRY} } * {NUMBER OF FILES}
```
The `.BIN` file stores file data in blocks of 2 KB for regular files and 12 KB for VAG audio files.  
When the `.DIR` file contains a file with a `{FILE SIZE}` value of 0 then the `.BIN` file contains one 2 KB block filled with `0x00`.  
When the `.DIR` file contains a file with a negative `{FILE SIZE}` value then the `.BIN` file does not contain any data for that file entry.  
Negative `{FILE SIZE}` values are only used for VAG audio files. 


### 3.1 `{FILE ENTRY}`

```
{SUM32 CHECKSUM}
{FILE DATA}
{BLANK SPACE}
```

`{SUM32 CHECKSUM}` is one DWORD containing the Sum32 checksum of the file.  
`{FILE DATA}` is the actual file data with a length of `{FILE SIZE}` plus some blank space filled with `0x00` up to the last block size (2 KB).


### 3.2 `{VAG FILE ENTRY}`

```
{ {VAG BLOCK HEADER} {VAG BLOCK DATA} [{ZEROED BYTES}] } * {BLOCK COUNT}
```

`{VAG BLOCK HEADER}` is `0x80` bytes long.  
`{VAG BLOCK DATA}` has a variable length specified in the `{VAG BLOCK HEADER}`.  
`{VAG BLOCK HEADER}`, `{VAG BLOCK DATA}` and `{ZEROED BYTES}` form a block which is `0x3000` bytes long (12 KB). 


#### 3.2.1 `{VAG BLOCK HEADER}`

**NOTE**  
This specification is incomplete

```
{BLOCK NUMBER}
{UNKNOWN 1}
{BLOCK SIZE}
{UNKNOWN 2}
```

`{BLOCK NUMBER}` is one WORD representing the blcok number starting from 0.  
`{BLOCK SIZE}` is one WORD representing the total block size (12 KB in most cases except for some last blocks).  
This size includes the `{VAG BLOCK HEADER}` size (`0x80` bytes).  

`{UNKNOWN 1}` is 2 bytes long. Usage is unknown.  
`{UNKNOWN 2}` is 122 bytes long. Usage is unknown.  


#### 3.2.2 `{VAG FILE DATA}`

**NOTE**  
This specification is incomplete

The VAG audio data is stored as mono (1 channel) raw compressed ADPCM.  
The sampling frequency of the VAG files is either 8000 or 11025 Hz.  

The size of `{VAG FILE DATA}` is `{VAG BLOCK HEADER}.{BLOCK SIZE} - 0x80`.
