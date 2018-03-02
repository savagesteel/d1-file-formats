Diablo 1 Level File Types Specification
=======================================

I) Description
--------------

	Diablo 1 levels depend on several files,

	Level graphics data longer than one byte (WORDs and DWORDs)
	is stored using little-endian byte order.


II) File Naming
---------------

	The level files are in levels/lXdata folders for Diablo
	and NLevels/lXdata for Hellfire, where X is:
	
		* 1 for Dungeon
		* 2 for Catacombs
		* 3 for Caves
		* 4 for Hell
		* 5 for Crypt
		* 6 for Nest
		* town for Tristram


II.1) SOL and AMP
-----------------



II.2) DUN
---------


III) SOL Level Files
--------------------


IV) AMP Level Files
-------------------


V) DUN Level Map Files
----------------------

V.1) File Structure
-------------------

	{DUN HEADER}
	{MAP LAYERS}


V.2) {DUN HEADER}
-----------------

	{MAP WIDTH}
	{MAP HEIGHT}
	
	{MAP WIDTH} and {MAP HEIGHT} are both one WORD long (2 bytes).


V.3) {MAP LAYERS}
-----------------
	
	{BASE LAYER}
	[{ITEMS LAYER}
	{MONSTERS LAYER}
	{OBJECTS LAYER}
	{UNUSED LAYER}]
	
	
V.3.1) {BASE LAYER}
-------------------

	{INCREMENTED TILE INDEX} * {MAP WIDTH} * {MAP HEIGHT}

	This layer is the base layer of the map, it defines the floor/walls/doors.
	The structure consists of {INCREMENTED TILE INDEX}es.
	Those indices define the level starting from top left tile 
	and ending with bottom right tile.
	The real tile index (in lX.til) is obtained with the following formula: 
	{TILE INDEX} = {INCREMENTED TILE INDEX} - 1.
	When {INCREMENTED TILE INDEX} is equal to 0 the default floor tile is used.

	
V.3.2) {ITEMS LAYER}
--------------------

	{ITEMS TABLE INDEX} * {MAP WIDTH} * {MAP HEIGHT} * 4
	
	This layer defines which items lie on the level ground.
	This layer is a sub-tile layer, which is 4 times the size 
	of the tile layer {BASE LAYER}.


V.3.3) {MONSTERS LAYER}
-----------------------

	{MONSTERS TABLE INDEX} * {MAP WIDTH} * {MAP HEIGHT} * 4
	
	This layer defines which monsters stand in the level.
	This layer is a sub-tile layer, which is 4 times the size 
	of the tile layer {BASE LAYER}.


V.3.4) {OBJECTS LAYER}
----------------------

	{OBJECTS INDEX} * {MAP WIDTH} * {MAP HEIGHT} * 4
	
	This layer defines which objects (chests, stands, etc.) 
	are positioned in the level.
	This layer is a sub-tile layer, which is 4 times the size 
	of the tile layer {BASE LAYER}.


V.3.5) {UNUSED LAYER}
---------------------

	{WORD} * {MAP WIDTH} * {MAP HEIGHT} * 4
	
	This layer is a sub-tile layer, which is 4 times the size 
	of the tile layer {BASE LAYER}.

	The use of this layer is unknown, only the following DUN level
	map files use it, with {WORD} values of 0 or 1 defining
	something looking like the base level layer shape:

		DIABDAT.MPQ:/levels/l1data/vile1.dun
		DIABDAT.MPQ:/levels/l1data/vile2.dun


V.X) Executable Hardcoded DUN Level maps
----------------------------------------

	Hellfire features hardcoded DUN level maps.
	
	The NaKrull DUN level map is located at the following offsets:

		Hellfire 1.00 US : 0x00091CC8
		Hellfire 1.00 EU : 0x00091AC8
		Hellfire 1.01 : 0x000942B0
		
	The data is 26 bytes long.
		
	The Cornerstone of the World DUN level map is located at the following offsets:
	
		Hellfire 1.00 US : 0x00091CE8
		Hellfire 1.00 EU : 0x00091AE8
		Hellfire 1.01 : 0x000942D0
		
	The data is 27 bytes long.
	
	These hardcoded DUN level maps differ from the regular DUN level files,
	instead using WORDs for storing data they use bytes.
	The first byte is the level map width, the second byte is the DUN level map height
	and then each byte is an incremented tile index.

	
V. Credits
----------

	Most of this document is based on the work of the following people:

	Ulmo
	Zamal & Zenda
