# Diablo 1 CEL File Format - Graphics

## I. Description

Diablo 1 CEL graphics files use the `.cel` file extension.

CEL graphics files contain one or more frames (images). A CEL graphics file needs a [color palette](PAL.md) file to be rendered.

CEL data longer than one byte (WORDs and DWORDs) is stored using little-endian byte order.

## II. File structure

```
{CEL HEADER}
{CEL FRAME} * {NUMBER OF CEL FRAMES}
```

## III. `{CEL HEADER}`

```
{NUMBER OF CEL FRAMES}
{CEL FRAME OFFSET} * {NUMBER OF CEL FRAMES}
{CEL FILE SIZE}
```

The `{NUMBER OF CEL FRAMES}` is one DWORD long.

Then there is one DWORD for each frame of the CEL graphics file, each indicating the `{CEL FRAME OFFSET}` where the frame data begins.

After the CEL frame offsets, comes one last DWORD containing the CEL graphics file size.

## IV. `{CEL FRAME}`

```
[{CEL FRAME HEADER}]
{CEL FRAME DATA}
```

### IV.1 `{CEL FRAME HEADER}`

The `{CEL FRAME}` can **optionally** start with a five WORDs `{CEL FRAME HEADER}`.

Each WORD of this header is an offset (relative to the beginning of the `{CEL FRAME}`) pointing to a 32 pixel lines block.
The first WORD is always `0x0A00` (`5 * WORD_SIZE = 10 = 0x000A`) because the first 32 pixel lines block starts immediately after the `{CEL FRAME HEADER}`.

**NOTE**

`{CEL FRAME}` width can be calculated based on the `{CEL FRAME HEADER}` and the number of pixels of the `{CEL FRAME}`.

For each WORD of the `{CEL FRAME HEADER}` the following formula can be used:

`{CEL FRAME WIDTH} = {CEL FRAME CHUNK NUMBER OF PIXELS} / 32`

`{CEL FRAME CHUNK NUMBER OF PIXELS}` is the number of pixels between two offsets (WORDs) of the `{CEL FRAME HEADER}`.

### IV.2 `{CEL FRAME DATA}`

A CEL frame is an image.
The frame data defines each pixel of the frame, starting from the bottom pixel line.
Each pixel line is defined from left to right.

There are two types of CEL graphics files : regular CEL files and level CEL files.
These types share the same `{CEL HEADER}` structure described in part III of this document.
Depending on the CEL type, they feature `{CEL FRAME HEADER}`s or not, and their pixel data is not always encoded the same way.

#### IV.2.1 Regular CEL files
	
Regular CEL files have one or more `{CEL FRAME}`.
Regular CEL files optionally feature `{CEL FRAME HEADER}`s.
Single frame CEL files do not feature a `{CEL FRAME HEADER}`.

Regular CEL files have frames encoded with the regular CEL pixel encoding.
Regular CEL pixel encoding defines pixel groups by using the following data structure:

```
{ENCODING BYTE}
[{PALETTE INDEX} * {PIXEL NUMBER}]
```

If `{ENCODING BYTE}` value is in the `[0x00, 0x7E]` range, then a number of `{ENCODING BYTE}` palette indices follow (see NOTE).
Each palette index defines the color of one pixel of the pixel line.

If `{ENCODING BYTE}` value is in the `[0x81, 0xFF]` range, then `256 - {ENCODING BYTE}` transparent pixels are added to the pixel line.

If `{ENCODING BYTE}` value is `0x7F`, then 127 (`0x7F`) palette indices follow, but the pixel line does not end, the next `{ENCODING BYTE}` will define pixels for the same pixel line. This is used to allow CEL frames with 128 pixels width or more.

If {ENCODING BYTE} value is 0x80, then 128 (256 - 0x80) transparent pixels follow, but the pixel line does not end.

Pixel groups are wrapped at the end of each pixel line, meaning that one pixel group can't overlap multiple lines.

**NOTE**

The fact that pixel groups are wrapped at the end of each line allows to calculate the width of `{CEL FRAMES}` which do not feature `{CEL FRAME HEADERS}`.

**NOTE**

When a CEL/CL2 graphics file refers to a palette color, 
it uses a one byte `{PALETTE INDEX}` in the `[0x00, 0xFF]` range.
This index refers to the `{PALETTE INDEX}`th color in the color palette.
To find the corresponding `{COLOR PALETTE OFFSET}` in the color palette file, use the following formula:

`{COLOR PALETTE OFFSET} = 3 * {PALETTE INDEX}`

**EXAMPLE**

DIABDAT.MPQ:/ctrlpan/panel8.cel
DIABDAT.MPQ:/ctrlpan/spelicon.cel
DIABDAT.MPQ:/data/inv/objcurs.cel

IV.2.2 Level CEL files
----------------------

Level CEL files contain typically more than 500 frames.
Each frame is 32 x 32 pixels.
Frame can be either coded using the regular encoding (as described in IV.2.1),
or with special encoding.
Special encoded frames have 0x220, 0x320 or 0x400 bytes size.
Special encoded frames do not use encoding bytes, instead they 
just contain palette indices and special 2 bytes sequences (0x00, 0x00)
indicating to fill the rest of the pixel line with transparent pixels.

<## EXAMPLE

DIABDAT.MPQ:/levels/l1data/l1.cel
DIABDAT.MPQ:/levels/l3data/l3.cel

##>


IV.2.2.1 Type 0 frames (upper wall) (0x400 bytes)
-------------------------------------------------

These frames do not have any transparency so they consist of
32*32 = 1024 = 0x400 palette indices:

{PALETTE INDEX} * 1024

<## EXAMPLE

DIABDAT.MPQ:/levels/l1data/l1.cel, frame 2

##>


IV.2.2.2 Type 1 frames
----------------------

Type 1 frames use the regular CEL encoding (see part IV.2.1).
They do not have a constant size.


IV.2.2.3 Type 2 and 3 frames (floor) (0x220 bytes)
--------------------------------------------------

These frames consist of a lower part and an
upper part, each with a 32 x 16 pixels size.
Type 2 frames have transparency on the left.
Type 3 frames have transparency on the right.

Lower part (0x120 bytes)
~~~~~~~~~~~~~~~~~~~~~~~~

The first (bottom) line of the lower part consists of 
30 transparent pixels and 2 opaque pixels, the second line of
28 transparent pixels and 4 opaque pixels and so on.
These 16 lines are encoded with the following structure:

{ {0x00,0x00}
{PALETTE INDEX} * {FIRST LINE PIXEL NUMBER}
{PALETTE INDEX} * {SECOND LINE PIXEL NUMBER} } * 8

or if the transparent part is on the right part of the frame:

{PALETTE INDEX} * 2
{0x00,0x00}

{ {PALETTE INDEX} * {FIRST LINE PIXEL NUMBER}
{PALETTE INDEX} * {SECOND LINE PIXEL NUMBER}
{0x00,0x00} } * 7

{PALETTE INDEX} * 32

If the special byte sequence {0x00,0x00} comes before
the palette indices then the lines start with transparent pixels.
If the special byte sequence {0x00,0x00} comes after
the palette indices then the lines ends with transparent pixels.


Upper part (0x100 bytes)
~~~~~~~~~~~~~~~~~~~~~~~~

The first line of the upper part consists of 
30 opaque pixels and 2 transparent pixels, the second line of
28 opaque pixels and 4 transparent pixels and so on.
These 16 lines are encoded with the following structure:

{ {0x00,0x00}
{PALETTE INDEX} * {FIRST LINE PIXEL NUMBER}
{PALETTE INDEX} * {SECOND LINE PIXEL NUMBER} } * 8

or if the transparent part is on the left part of the frame:

{PALETTE INDEX} * 30
{0x00,0x00}

{ {PALETTE INDEX} * {FIRST LINE PIXEL NUMBER}
{PALETTE INDEX} * {SECOND LINE PIXEL NUMBER}
{0x00,0x00} } * 7

Last line is not encoded, it consists of 32 transparent pixels.

<## EXAMPLE

DIABDAT.MPQ:/levels/l1data/l1.cel, frame 6
DIABDAT.MPQ:/levels/l1data/l1.cel, frame 7

##>


IV.2.2.4  Type 4 and 5 frames (wall bottom) (0x320 bytes)
---------------------------------------------------------

These frames consist of a lower part and an
upper part, each with a 32 x 16 pixels size.
Type 4 frames have transparency on the left.
Type 5 frames have transparency on the right.

Lower part (0x120 bytes)
~~~~~~~~~~~~~~~~~~~~~~~~

The lower part of these 0x320 bytes frames is
the same than the lower part of 0x220 bytes frames
(see part IV.2.2.3).


Upper part (0x200 bytes)
~~~~~~~~~~~~~~~~~~~~~~~~

The upper part does not have any transparency so they
consist of 32*16 = 512 = 0x200 palette indices:

{PALETTE INDEX} * 512	

<## EXAMPLE

DIABDAT.MPQ:/levels/l1data/l1.cel, frame 11
DIABDAT.MPQ:/levels/l1data/l1.cel, frame 19

##>


V. Compiled CEL files
---------------------

Some CEL files are in fact a compilation of multiple CEL files,
thus the header is as follows:

{CEL OFFSET} * {NUMBER OF CEL}

After this header, at each {CEL OFFSET} comes a CEL file 
with the structure described in part II.

<## EXAMPLE

DIABDAT.MPQ:/towners/animals/cow.cel
DIABDAT.MPQ:/towners/smith/smithw.cel

##>


VI. Credits
-----------

Most of this document is based on the work of the following people:

Honi B
ArthurDent
Zamal & Zenda
