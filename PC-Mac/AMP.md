# Diablo 1 AMP File Format - Automap Flags

[1. Description](#1-description)  
[2. File structure](#2-file-structure)  


## 1. Description

Diablo 1 AMP metadata file use the `.amp` file extension.  
The AMP metadata file contains one WORD (2 bytes) long automap-flags for the tiles.  


## 2. File Structure

```
{TILE FLAG} {NUMBER OF TILES}
```

The lower/first byte of the {TILE FLAG} define the main automap-type of the tile:
- `0x0` : Type 0, tile without any vertical or horizontal feature
- `0x1` : Type 1, stand-alone column or other unpassable object
- `0x2` : Type 2, open ended vertical features on the left side
- `0x3` : Type 3, open ended horizontal features on the top side
- `0x4` : Type 4, vertical features on the left side and horizontal features on the top side
- `0x5` : Type 5, closed vertical features on the left side
- `0x6` : Type 6, closed horizontal features on the top side
- `0x7` : Type 7, tile without any vertical or horizontal feature (possible stand-alone column)
- `0x8` : Type 8, vertical features on the left side and horizontal features on the bottom side
- `0x9` : Type 9, vertical features on the right side and horizontal features on the top side
- `0xA` : Type 10, horizontal features on the bottom side
- `0xB` : Type 11, vertical features on the right side
- `0xC` : Type 12, vertical features on the right side and horizontal features on the bottom side
- `0xD` to `0xFF` : Unused

The higher/second byte of the {TILE FLAG} have the following meaning (starting from lowest bit):
- `1` : door on the left or bottom side
- `2` : door on the top or right side
- `3` : vertical arch (on the left side)
- `4` : horizontal arch (on the top side)
- `5` : vertical grating (on the left side)
- `6` : horizontal grating (on the top side)
- `7` : external, unreachable (barrier) tile
- `8` : stairs
