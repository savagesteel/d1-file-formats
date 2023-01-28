# Diablo 1 SOL File Format - Subtile Flags

[1. Description](#1-description)  
[2. File structure](#2-file-structure)  


## 1. Description

Diablo 1 SOL metadata file use the `.sol` file extension.  
The SOL metadata file contains flags (one BYTE) for the subtiles.  


## 2. File Structure

```
{SUBTILE FLAG} * {NUMBER OF SUBTILES}
```

The bits of the {SUBTILE FLAG} have the following meaning (starting from lowest bit):
- `1` : blocks the walk-path
- `2` : blocks the light
- `3` : blocks missiles
- `4` : enable transparency
- `5` : left floor {CEL FRAME} has transparent part
- `6` : right floor {CEL FRAME} has transparent part
- `7` : ignore the two bits above (unused)
- `8` : can be used as trap source and works as background for shrines and bookcases
