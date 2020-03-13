# Diablo 1 PAL File Format - Color Palettes

[1. Description](#1-description)  
[2. File structure](#2-file-structure)  
[3. `{PALETTE COLOR}` (3 bytes)](#3-palette-color-3-bytes)  


## 1. Description

Diablo 1 color palettes use the `.pal` file extension.  
Color palettes are sets of 256 colors used to render CEL/CL2 graphics.


## 2. File structure

```
{PALETTE COLOR} * 256
```


## 3. `{PALETTE COLOR}` (3 bytes)

```
{RED COMPONENT} {GREEN COMPONENT} {BLUE COMPONENT}
```

Each color is 3 bytes. First byte is the red color component, second is the green color component and third is the blue color component.

When a CEL/CL2 graphics file refers to a palette color, it uses a one byte `{PALETTE INDEX}` in the `[0x00, 0xFF]` range.  
This index refers to the `{PALETTE INDEX}`th color in the color palette.  
To find the corresponding `{COLOR PALETTE OFFSET}` in the color palette file, use the following formula:

```
{COLOR PALETTE OFFSET} = 3 * {PALETTE INDEX}
```


## TODO
- Add palette structure
    - First half (128 bytes) background
    - Second half (128 bytes) other elements
    - Except for cuts
- Add hue and brightness
    - Each half (128 bytes) has 8 hues
    - Each hue (16 bytes) has 16 levels of brightness (1 byte each)
        - Every square on the map has a light level which is combined with the orignal light level to get the final color (to be detailed).
    - Is it different for the first 32 bytes of the second half?
        - It seems that those 32 bytes are split into 4 hues (8 bytes) with 8 levels of brightness (1 byte each)
    - It is different for level loading screens
- Add JASC palette format?
