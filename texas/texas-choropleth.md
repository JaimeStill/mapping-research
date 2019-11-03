# Texas Choropleth

This document outlines the steps specified in Mike Bostock's [Command Line Cartography](https://medium.com/@mbostock/command-line-cartography-part-1-897aa8f8ca2c) walkthroughs, but relevant to Texas rather than California. It assumes all of the installed tools outlined at the beginning of the [README](../readme.md).

1. Acquire Shape Data and Unzip

```
curl "https://www2.census.gov/geo/tiger/GENZ2014/shp/cb_2014_48_tract_500k.zip" -o source\cb_2014_48_tract_500k.zip

7z e source\cb_2014_48_tract_500k.zip
```

2. Convert shapefile to GeoJSON

```
shp2json source\cb_2014_48_tract_500k.shp -o data\tx.json
```

3. Apply projection

```
geoproject "d3.geoConicConformal().parallels([30 + 70 / 60, 31 + 53 / 60]).rotate([100 + 20 / 60, 0]).fitSize([960, 960], d)" < 
data\tx.json > data\tx-albers.json
```

4. Generate preview

```
geo2svg -w 960 -h 960 < data\tx-albers.json > images\tx-albers.svg
```

[![Texas](./images/tx-albers.svg)](./images/tx-albers.svg)

5. Split features into ndjson

```
ndjson-split "d.features" < data\tx-albers.json > data\tx-albers.ndjson
```

6. Append `id` to features ndjson

```
ndjson-map "d.id = d.properties.GEOID.slice(2), d" < data\tx-albers.ndjson > data\tx-albers-id.ndjson
```

7. Download the Total Population Estimate, filtered by Texas (FIPS 48)

```
curl "https://api.census.gov/data/2014/acs/acs5/profile?get=DP02_0001E&for=tract:*&in=state:48&key=4c66d1d462c6ea53f7e506d3656fb61b7cd7d2f4" -o data\cb_2014_48_tract_B01003.json
```

8. Join population data to geometry using ndjson-join

```
ndjson-join "d.id" data\tx-albers-id.ndjson data\cb_2014_48_tract_DP02_0001E.ndjson > data\tx-albers-join.ndjson 
```

9. Compute population density using ndjson-map and remove unnecessary properties

> Note that the constant **2589975.2356** = <strong>1609.34<sup>2</sup></strong> converts the land area from square meters to square miles.

```
ndjson-map "d[0].properties = {density: Math.floor(d[1].DP02_0001E / d[0].properties.ALAND * 2589975.2356) }, d[0]" < data\tx-albers-join.ndjson > data\tx-albers-density.ndjson
```

10. Convert back to GeoJSON using ndjson-reduce and ndjson-map

```
ndjson-reduce < data\tx-albers-density.ndjson | ndjson-map "{type: \"FeatureCollection\", features: d}" > data\tx-albers-density.json
```

or simply with ndjson-reduce:

```
ndjson-reduce "p.features.push(d), p" "{type: \"FeatureCollection\", features: []}" < data\tx-albers-density.ndjson > data\tx-albers-density.json
```

11. Define a fill property using a sequential scale with the Viridis color scheme

```
ndjson-map -r d3 "(d.properties.fill = d3.scaleSequential(d3.interpolateViridis).domain([0, 3200])(d.properties.density), d)" < data\tx-albers-density.ndjson > data\tx-albers-color.ndjson
```

12. Generate a preview image using geo2svg

```
geo2svg -n --stroke none -p 1 -w 960 -h 960 < data\tx-albers-color.ndjson > images\tx-albers-color.svg
```

[![Texas Population](./images/tx-albers-color.svg)](./images/tx-albers-color.svg)

