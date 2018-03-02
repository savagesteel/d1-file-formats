# Diablo 1 PAL File Format
# Color Palette

# I. Description

Diablo 1 color palettes use the .pal file extension.
Color palettes are sets of 256 colors.
Color palettes are used to render CEL/CL2 graphics.

# II. File structure

`{PALETTE COLOR} * 256`

**The first half of the palette depends on the level palette ??**

# III. `{PALETTE COLOR}` (3 bytes)

`{RED COMPONENT}
{GREEN COMPONENT}
{BLUE COMPONENT}`

Each color is 3 bytes. First byte is the red color component,
second is the green color component and third is the blue color component

When a CEL/CL2 graphics file refers to a palette color, 
it uses a one byte `{PALETTE INDEX}` in the `[0x00, 0xFF]` range.
This index refers to the `{PALETTE INDEX}`th color in the color palette.
To find the corresponding `{COLOR PALETTE OFFSET}`
in the color palette file, use the following formula:

`{COLOR PALETTE OFFSET} = 3 * {PALETTE INDEX}`
