# Diablo 1 CL2 File Format - Graphics

[1. Description](#1-description)  
[2. File structure](#2-file-structure)  
[3. `{CL2 HEADER}`](#3-cl2-header)  
[3.1 `{CL2 GROUP HEADER}`](#31-cl2-group-header)  
[3.2 `{CL2 CLIP HEADER}`](#32-cl2-clip-header)  
[4. `{CL2 FRAME}`](#4-cl2-frame)  
[4.1 `{CL2 FRAME HEADER}`](#41-cl2-frame-header)  
[4.2 `{CL2 FRAME DATA}`](#42-cl2-frame-data)  
[5. Credits](#5-credits)  


## 1. Description

Diablo 1 CL2 graphics files use the `.cl2` file extension.  
CL2 graphics files contain multiple frames (images).  
Also CL2 graphics files often contain multiple groups of frames.  
A CL2 graphics file needs a color palette file to be rendered.

CL2 data longer than one byte (WORDs and DWORDs) is stored using little-endian byte order.


## 2. File structure

```
{CL2 HEADER}
{CL2 FRAME} * {NUMBER OF CL2 FRAMES}
```


## 3. `{CL2 HEADER}`

```
[{CL2 GROUP HEADER}]
{CL2 CLIP HEADER}
```

There are two types of CL2 graphics files : mono-group CL2 files and multi-group CL2 files.  
Mono-group CL2 files do not feature `{CL2 GROUP HEADER}` in the `{CL2 HEADER}`.


### 3.1 `{CL2 GROUP HEADER}`

`{CL2 GROUP OFFSET} * {NUMBER OF CL2 GROUPS}`

The `{CL2 GROUP HEADER}` consists of one DWORD for each group.  
Each DWORD is a `{CL2 GROUP OFFSET}` indicating where the `{CL2 CLIP HEADER}` of the group starts.


### 3.2 `{CL2 CLIP HEADER}`

```
{NUMBER OF CL2 FRAMES IN GROUP}
{CL2 FRAME OFFSET} * {NUMBER OF CL2 FRAMES IN GROUP}
{NEXT CL2 GROUP OFFSET}
```

The `{NUMBER OF CL2 FRAMES IN GROUP}` is one DWORD long.  
Then there is one DWORD for each frame of the CL2 frame group, each indicating the `{CL2 FRAME OFFSET}` where the frame data begins.  
After the CL2 frame offsets, comes one last DWORD containing offset of the first frame of the next group of the CL2 graphics file.  
For the last frame, this offset contains the CL2 graphics file size.


## 4. `{CL2 FRAME}`

```
{CL2 FRAME HEADER}
{CL2 FRAME DATA}
```


### 4.1 `{CL2 FRAME HEADER}`

The `{CL2 FRAME}` always starts with a five WORDs `{CL2 FRAME HEADER}`.  
Each WORD of this header is an offset (relative to the beginning of the `{CL2 FRAME}`) pointing to a 32 pixel lines block.  
The first WORD is always `0x0A00` (`5 * WORD_SIZE = 10 = 0x000A`) because the first 32 pixel lines block starts immediately after the `{CL2 FRAME HEADER}`.

**NOTE**

`{CL2 FRAME}` width is calculated based on the `{CL2 FRAME HEADER}` and the number of pixels of the `{CL2 FRAME}`.  
For each WORD of the `{CL2 FRAME HEADER}` the following formula can be used:

```
{CL2 FRAME WIDTH} = {CL2 FRAME CHUNK NUMBER OF PIXELS} / 32
```

`{CL2 FRAME CHUNK NUMBER OF PIXELS}` is the number of pixels between two offsets (WORDs) of the `{CL2 FRAME HEADER}`.


### 4.2 `{CL2 FRAME DATA}`

A CL2 frame is an image.  
The frame data defines each pixel of the frame, starting from the bottom left pixel and ending with the top right pixel.

Pixels are encoded as follow:

```
{ENCODING BYTE}
[{PALETTE INDEX} || {PALETTE INDEX} * {PIXEL NUMBER}]
```

If `{ENCODING BYTE}` value is in the `[0x01, 0x7F]` range, then `{ENCODING BYTE}` transparent pixels are added.  
If `{ENCODING BYTE}` value is in the `[0x80, 0xBE]` range, then only one `{PALETTE INDEX}` follows. `191 - {ENCODING BYTE}` pixels with this `{PALETTE INDEX}` color are added.  
If `{ENCODING BYTE}` value is in the `[0xBF, 0xFF]` range, then a number of `256 - {ENCODING BYTE}` palette indices follow (see NOTE).

**NOTE**

When a CEL/CL2 graphics file refers to a palette color, it uses a one byte `{PALETTE INDEX}` in the `[0x00, 0xFF]` range.  
This index refers to the `{PALETTE INDEX}`th color in the color palette.  
To find the corresponding `{COLOR PALETTE OFFSET} ` in the color palette file, use the following formula:

```
{COLOR PALETTE OFFSET} = 3 * {PALETTE INDEX}
```


## 5. Credits

Most of this document is based on the work of the following people:
- Honi B
- ArthurDent
- Zamal & Zenda
