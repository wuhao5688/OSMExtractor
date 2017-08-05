# OSMExtractor

The extractor for parsing raw map data from [OpenStreetMap](http://www.openstreetmap.org/export).

## Usage

- Select your interested area in [OpenStreetMap](http://www.openstreetmap.org/export).
- Download the raw map data (Overpass API suggested) which is actually in XML-format.
- Compile the project or directly run the binary executable file which is located in `bin\Release\`.

PS：I've included a tiny sample map crawled from OSM which is in the name of `sample.xml` in `bin\Release\`. Have a try :)

## Enviornment

- Language:  C#
- IDE:  Microsoft Visual Studio 2013
- OS:  Windows 7 64-bit with SP1

The code is tested to be runnable in Aug 5th, 2017.

## Parsed Data format

### nodeOSM.txt

Format: `[NodeID]`\t`[latitude]`\t`[longitude]`

One node/vertex per line with increasing (continuous) ids.
E.g.,
```
0   41.1689665  -8.6444747
1   41.1658735  -8.6444774
2   41.1670798  -8.6424338
3   41.1673856  -8.642543
4   41.1669776  -8.6417132
5   41.1676312  -8.6424866
...
1000   41.1575375  -8.6443184
```
, which records 1,000 vertices in the road network.

### edgeOSM.txt

Format: `[EdgeId]`\t`[StartNodeId]`\t`[EndNodeId]`\t`[k]`\t`[lat1]`\t`[lon1]`\t`[lat2]`\t`[lon2]`...\t`[latk]`\t`[lonk]`

One edge (`StartNode` -> `EndNode`) per line with increasing (continuous) ids.
And `k` refers to the number of points of a polyline representing the shape of the road (including the start and the end node).
I.e., `(lat1, lon1)` is just the coordinate of `StartNode`, and `(latk, lonk)` is the coordinate of `EndNode`.

E.g.,
```
0   0   1326    5   41.1689665  -8.6444747  41.1688112  -8.6443785  41.1685579  -8.6440804  41.1683059  -8.6438068  41.1680768  -8.6437482
1   4   5   2   41.1669776  -8.6417132  41.1676312  -8.6424866
...
1500    1499    1494    2   41.1849529  -8.6317477  41.185196   -8.6318118
```
, which records 1,500 edges in the road network.
Edge `0` represents an edge from node `0` to node `1326` with `5` points representing the shape of the road as a 
polyline. And edge `1` represents an edge from node `4` to `5` with `2` points representing the shape, which means the 
shape of this road is a straight line segment (i.e., the first point`(41.1669776, -8.6417132)` is just the coordinate of 
node `4` and the second point `(41.1676312, -8.6424866)` is just the coordinate of node `5`).

### wayType

Format: `EdgeId`\t`WayTypeName`\t`WayTypeId`

One edge per line with increasing (continous) ids. The `WayTypeName` also includes the "_link" name, e.g., `motorway_link`. The `wayTypeId` of 
those "_link" way is identical to their corresponding non-"_link" way's `wayTypeId`, e.g., the `wayTypeId` of `motorway_link` is identical to 
the one of `motorway`, i.e., 7.

E.g.
```
0   motorway_link   7
1   motorway    7
2   motorway    7
3   trunk_link  6
4   residential 1
5   residential 1
...
1500    tertiary    3
```
, which records the corresponding way types of 1,500 edges.

The relations between `wayTypeName` and `wayTypeId` are shown as follows. They range from the most (7) to least (0) important. 
For more detail, please refer to the official document in  [wiki](http://wiki.openstreetmap.org/wiki/Map_Features#Highway).
Note that there exist many other types of roads in OSM, while I only selected those types which are in major and can be passed by vehicles.
```
7: motorway
6: trunk
5: primary
4: secondary
3: tertiary
2: unclassified
1: residenotial

7: motorway_link
6: trunk_link
5: primary_link
4: secondary_link
3: tertiary_link
```