# Diablo 1 DUN File Format - Level Maps

[1. Description](#1-description)  
[2. File structure](#2-file-structure)  
[3. `{DUN HEADER}`](#3-dun-header)  
[4. `{MAP LAYERS}`](#4-map-layers)  
[4.1 `{BASE LAYER}`](#41-base-layer)  
[4.2 `{ITEMS LAYER}`](#42-items-layer)  
[4.3 `{MONSTERS LAYER}`](#43-monsters-layer)  
[4.4 `{OBJECTS LAYER}`](#44-objects-layer)  
[4.5 `{UNUSED LAYER}`](#45-unused-layer)  
[5. Executable Hardcoded DUN Level Maps](#5-executable-hardcoded-dun-level-maps)  
[6. Credits](#6-credits)


## 1. Description

Diablo 1 DUN level maps use the `.dun` file extension.  
The DUN level maps contain portions of levels used by the random level generation algorithm.  
Some of these files contain generic level layouts, other contain quest specific level layouts.

Level maps data longer than one byte (WORDs and DWORDs)	is stored using little-endian byte order.


## 2. File Structure

```
{DUN HEADER}
{MAP LAYERS}
```


## 3. `{DUN HEADER}`

```
{MAP WIDTH}
{MAP HEIGHT}
```

`{MAP WIDTH}` and `{MAP HEIGHT}` are both one WORD long (2 bytes).


## 4. `{MAP LAYERS}`

```
{BASE LAYER}
[{ITEMS LAYER}
{MONSTERS LAYER}
{OBJECTS LAYER}
{TRANSVAL LAYER}]
```

Each INDEX is one WORD long (2 bytes).

### 4.1 `{BASE LAYER}`

```
{INCREMENTED TILE INDEX} * {MAP WIDTH} * {MAP HEIGHT}
```

This layer is the base layer of the map, it defines the floor/walls/doors.  
The structure consists of `{INCREMENTED TILE INDEX}`es.  
Those indices define the level starting from top left tile and ending with bottom right tile.  
The real tile index (in `lX.til`) is obtained with the following formula:

```
{TILE INDEX} = {INCREMENTED TILE INDEX} - 1
```

When `{INCREMENTED TILE INDEX}` is equal to 0 the default floor tile is used.


### 4.2 `{ITEMS LAYER}`

```
{ITEMS TABLE INDEX} * {MAP WIDTH} * {MAP HEIGHT} * 4
```

This layer defines which items lie on the level ground.  
This layer is a sub-tile layer, which is 4 times the size of the tile layer `{BASE LAYER}`.


### 4.3 `{MONSTERS LAYER}`

```
{MONSTERS TABLE INDEX} * {MAP WIDTH} * {MAP HEIGHT} * 4
```

This layer defines which monsters stand in the level.  
This layer is a sub-tile layer, which is 4 times the size of the tile layer `{BASE LAYER}`.


### 4.4 `{OBJECTS LAYER}`

```
{OBJECTS INDEX} * {MAP WIDTH} * {MAP HEIGHT} * 4
```

This layer defines which objects (chests, stands, etc.) are positioned in the level.  
This layer is a sub-tile layer, which is 4 times the size of the tile layer `{BASE LAYER}`.


### 4.5 `{TRANSVAL LAYER}`

```
{TRANSVAL INDEX} * {MAP WIDTH} * {MAP HEIGHT} * 4
```

This layer defines the transval index (effectively the room index) of the given sub-tile.
This layer is a sub-tile layer, which is 4 times the size of the tile layer `{BASE LAYER}`.
In vanilla diablo this part is ignored, except for the set-level of vile-betrayer:

```
DIABDAT.MPQ:/levels/l1data/vile1.dun
```


## 5. Executable Hardcoded DUN Level Maps

Hellfire features hardcoded DUN level maps.

The NaKrull DUN level map is located at the following offsets:
- Hellfire 1.00 US : `0x00091CC8`
- Hellfire 1.00 EU : `0x00091AC8`
- Hellfire 1.01 : `0x000942B0`
The data is 26 bytes long.

The Cornerstone of the World DUN level map is located at the following offsets:
- Hellfire 1.00 US : `0x00091CE8`
- Hellfire 1.00 EU : `0x00091AE8`
- Hellfire 1.01 : `0x000942D0`
The data is 27 bytes long.

These hardcoded DUN level maps differ from the regular DUN level files, instead using WORDs for storing data they use bytes.  
The first byte is the level map width, the second byte is the DUN level map height and then each byte is an incremented tile index.


## 6. Credits

Most of this document is based on the work of the following people:
- Ulmo
- Zamal & Zenda
