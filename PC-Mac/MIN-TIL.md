# Diablo 1 MIN and TIL File Formats - Level Graphics

[1. Description](#1-description)  
[2. File Naming]()  
[2.1 CEL Graphics Files]()  
[2.2 Color Palettes]()  
[2.3 MIN and TIL Level Graphics Files]()  
[3. MIN Graphics Files]()  
[3.1 File Structure]()  
[3.2 `{SUB-TILE DATA}`]()  
[3.3 `{CEL FRAME REFERENCE}`]()  
[4. TIL Graphics Files]()  
[4.1 File Structure]()  
[4.2 `{TILE DATA}`]()  
[5. Credits](#5-credits)  


## 1. Description

Diablo 1 level graphics depend on several files, first it depends on specific [CEL graphics](CEL.md) files and [color palettes](PAL.md) which contain the actual pixel data in the form of small 32 x 32 pixels frames.  
Secondly it relies on sub-tiles with `.min` file extension and tiles with `.til` file extension. Tiles and sub-tiles define layouts of 32 x 32 pixels frames which result in larger frames representing level parts.

Level graphics data longer than one byte (WORDs and DWORDs) is stored using little-endian byte order.


## 2. File Naming

The level files are in `levels/lXdata` folders for Diablo and `NLevels/lXdata` for Hellfire, where `X` is:

- `1` for Dungeon
- `2` for Catacombs
- `3` for Caves
- `4` for Hell
- `5` for Crypt (Hellfire)
- `6` for Nest (Hellfire)
- `town` for Tristram


### 2.1 CEL Graphics Files

Level CEL graphics files are named as follows:
- `lX.cel`

Those files contains 32x32 pixels frames (as described in part IV.2.2 of the [CEL graphics](CEL.md) file format specification).

In addition the following CEL graphics files are used to store archs graphics:
- `l1s.cel`
- `l2s.cel`
- `l5s`
- `towns.cel`

Those files contains eight 64 x 160 pixels frames.


### 2.2 Color Palettes

Level color palettes are named as follows:
- `lX.pal`
- `lX_N.pal` with `N` an integer in the `[1,5]` range
- `lXpalg.pal`
- `l3_w.pal`
- `l3pfoul.pal`
- `l3pwater.pal`
- `l3_i.pal`

`lX.pal` and `lX_1.pal` are identical.

`lX_N.pal` files are used for alternative color schemes.  
The Crypt do not use alternative color schemes, only `l5.pal` exists.

`lXpalg.pal` files are used to display levels in grayscale color scheme.

`l3_w.pal` and `l3pwater.pal` are identical.  
`l3pfoul.pal` and `l3pwater.pal` are used for the Poisoned Water Supply level,`l3pfoul.pal` is used to display poisoned yellow water and `l3pwater.pal` is used to display clean blue water.

`l3_i.pal` is an unused color palette which can display caves with an iced look, i.e. the magma looks like frozen water and the rocks have a blue/ice color scheme.


### 2.3 MIN and TIL Level Graphics Files

Level graphics files are named as follows:
- `lX.min`
- `lX.til`


## 3. MIN Graphics Files

Those files contain the layout of CEL frames for a level square.  
A level square is for example the square where your character stands.  
A square has a 64 x 32 pixels size (two 32 x 32 pixels frames from the `lX.cel` file).  
But a MIN graphics file does not only defines the pattern of the ground, it also defines anything built on this level square (e.g. a wall).  
The resulting image is called a sub-tile.  
Sub-tiles can either have a 64 x 160 pixels size (Dungeon, Catacombs, Caves, Nest, Crypt) or a 64 x 256  pixels size (Town and Hell).  


### 3.1 File Structure

```
{SUB-TILE DATA} * {NUMBER OF SUB-TILES}
```


### 3.2 `{SUB-TILE DATA}`

```
{CEL FRAME REFERENCE} * 10
```

or

```
{CEL FRAME REFERENCE} * 16
```

Depending on the level number, the sub-tile consists of either 10 or 16 `{CEL FRAME REFERENCE}`.  
Dungeon, Catacombs, Caves, Nest and Crypt feature 10 `{CEL FRAME REFERENCE}`.  
Town and Hell feature 16 `{CEL FRAME REFERENCE}`.  
Those references define the resulting sub-tile starting from top left CEL frame and ending with bottom right CEL frame.


### 3.3 `{CEL FRAME REFERENCE}`

```
{CEL FRAME TYPE} {INCREMENTED CEL FRAME INDEX}
```

A `{CEL FRAME REFERENCE}` is one WORD long (2 bytes).  
The first 4 bits represent the `{CEL FRAME TYPE}`:

- `0x0` : Type 0 level frame, see part 4.2.2.1 of the [CEL graphics](CEL.md#4221-type-0-frames-upper-wall-0x400-bytes) file format specification).
- `0x1` : Type 1 level frame, see part 4.2.2.2 of the [CEL graphics](CEL.md#4222-type-1-frames) file format specification).
- `0x2` : Type 2 level frame, see part 4.2.2.3 of the [CEL graphics](CEL.md#4223-type-2-and-3-frames-floor-0x220-bytes) file format specification).
- `0x3` : Type 3 level frame, see part 4.2.2.3 of the [CEL graphics](CEL.md#4223-type-2-and-3-frames-floor-0x220-bytes) file format specification).
- `0x4` : Type 4 level frame, see part 4.2.2.4 of the [CEL graphics](CEL.md#4224-type-4-and-5-frames-wall-bottom-0x320-bytes) file format specification).
- `0x5` : Type 5 level frame, see part 4.2.2.4 of the [CEL graphics](CEL.md#4224-type-4-and-5-frames-wall-bottom-0x320-bytes) file format specification).
- `0x6` to `0xF` : Unused

The last 12 bits represent the `{INCREMENTED CEL FRAME INDEX}`.  
The real index in `lX.cel` CEL graphics file is obtained with the following formula:

```
{CEL FRAME INDEX} = {INCREMENTED CEL FRAME INDEX} - 1
```

When `{INCREMENTED CEL FRAME INDEX}` is equal to 0 no CEL frame is added to the sub-tile, instead the 32 x 32 pixels area is transparent.


## 4. TIL Graphics Files

Those files define layouts of 4 (`2*2`) sub-tiles (see part III).  
The resulting image is called a tile.  

Tiles can have either a size of 128 x 192 pixels size (Dungeon, Catacombs, Caves, Nest and Crypt) or 128 x 288 pixels size (Town and Hell).  
Sub-tiles coordinates from top left corner of the tile, in pixels are:
- 1rst sub-tile : `(32, 0)`
- 2nd sub-tile  : `(64, 16)`
- 3rd sub-tile  : `(0,  16)`
- 4th sub-tile  : `(32, 32)`


### 4.1 File Structure

```
{TILE DATA} * {NUMBER OF TILES}
```


### 4.2 `{TILE DATA}`

```
{SUB-TILE INDEX} * 4
```

A `{SUB-TILE INDEX}` is one WORD long (2 bytes).  
To obtain the real sub-tile index, add 1 to `{SUB-TILE INDEX}`.  

Those indices define the resulting tile starting from top left sub-tile and ending with bottom right sub-tile.


## 5. Credits

Most of this document is based on the work of the following people:
- Ulmo
- Zamal & Zenda
