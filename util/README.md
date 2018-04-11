# Comparing `minjur` and `osmium export`


![Compare Map](compare-style.png)
Features only in osmium-export are in green, features only from minjur are red.

### [View this map](https://api.mapbox.com/styles/v1/jenningsanderson/cjfuef4kt0evh2rqhdyny5rau.html?fresh=true&title=true&access_token=pk.eyJ1IjoiamVubmluZ3NhbmRlcnNvbiIsImEiOiIzMHZndnpvIn0.PS-j7fRK3HGU7IE8rbLT9A#8.1/40.891/-73.833)

## Usage
First, create `tmp.mbtiles` with osmium export, using the osmium config in the parent directory:

In this case, `new_york.osm.pbf` was cut from the same `planet-latest.osm.pbf` that the latest osm-qa-tiles file was built from:

```
osmium export -r /data/planet/new_york.osm.pbf \
	-c ../osm-qa-tile.osmiumconfig -f geojsonseq \
	-u type_id | tippecanoe -Z12 -z12 \
		--no-tile-stats -o tmp.mbtiles -f -ps -pf -pk -b0 -d20 -l osm
```

Install the dependencies (tile-reduce) and then run tile-reduce:

```
npm install
node index.js
```

This will create two files: `missing-in-new.geojsonl` and `missing-in-old.geojsonl`, with the features that are present in one tileset and not in the other.

Tile those for inspection: 


```
tippecanoe -Pf -Z8 -z12 -o tiles2.mbtiles \
	--no-tile-stats  \
	--named-layer=oldOnly:missing-in-new.geojsonl \
	--named-layer=newOnly:missing-in-old.geojsonl
```

Using something like [`mbview`](//github.com/mapbox/mbview), you can see the difference:

![comparison-new-v-old.png]