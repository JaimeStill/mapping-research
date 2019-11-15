# Mapping Research

* [Converting Shapefiles](#converting-shapefiles)
    * [Install npm Packages](#install-npm-packages)
    * [Converting to GeoJSON](#converting-to-geojson)
    * [Applying a Projection](#applying-a-projection)
    * [Generating an SVG Preview](#generating-an-svg-preview)
    * [Use ndjson-split To Create a Stream from GeoJSON Array Property](#use-ndjson-split-to-create-a-stream-from-geojson-array)
    * [Use ndjson-map to Set Properties](#use-ndjson-map-to-set-properties)
* [Tools](#tools)
* [Resources](#resources)
* [References](#references)

> All command line examples are executed from the root of this repo.

## Converting Shapefiles
[Back to Top](#mapping-research)

The shapefiles contained in the `source` directory were obtained from the open source [NaturalEarth](https://www.naturalearthdata.com/downloads/) repository. It contains [1:50m cultural vectors](https://www.naturalearthdata.com/downloads/50m-cultural-vectors/) for admin 0 and admin 1 (countries and states).

### Install npm Packages
[Back to Top](#mapping-research)

```
npm install -g shapefile
npm install -g d3
npm install -g d3-geo-projection
npm install -g d3-scale-chromatic
npm install -g ndjson-cli
npm install -g topojson
```  

Make sure that the map data is unzipped in a safe location, and create a directory that you will store the converted map data. In this directory structure is as follows:

* `source` contains the zipped and unzipped data downloaded from Natural Earth
* `data` contains the JSON files converted from the shapefile data
* `images` contains SVGs for previewing the converted JSON

### Converting to GeoJSON
[Back to Top](#mapping-research)

From the command line:

```
shp2json <file>.shp -o <file>.json
```

Example:

```
shp2json source\ne_50m_admin_0_countries\ne_50m_admin_0_countries.shp -o data\countries.json
```

### Applying a Projection
[Back to Top](#mapping-research)

> In this section, you can use any of the projections specified in the [d3-geo-projection docs](https://github.com/d3/d3-geo-projection/blob/master/README.md)

From the command line:

```
geoproject "<projection>.fitSize([<width>, <height>], d)" < <input>.json > <output>.json
```

Example:

```
geoproject "d3.geoArmadillo().parallel([0]).fitSize([960, 960], d)" < data\countries.json > data\countries-armadillo.json
```

### Generating an SVG Preview
[Back to Top](#mapping-research)

From the command line:

```
geo2svg -w <width> -h <height> < <input>.json > <output>.svg
```

Example:

```
geo2svg -w 960 -h 960 < data\countries-armadillo.json > images\countries-armadillo.svg
```

### Use ndjson-split To Create a Stream from GeoJSON Array Property
[Back to Top](#mapping-research)

From the command line:

```
ndjson-split "d.<array>" < <input>.json > <output>.ndjson
```

Example:

> Create a new file of objects rooted in each array of features in the source file

```
ndjson-split "d.features" < data\countries-albers.json > data\countries-albers.ndjson
```

### Use ndjson-map to Set Properties
[Back to Top](#mapping-research)

From the command line:

```
ndjson-map "d.<prop> = d.<object>.<property>, d" < <input>.ndjson > <output>.ndjson
```

Example:

> Add a root `id` property to each object lifted from `object.properties.NE_ID`

```
ndjson-map "d.id = d.properties.NE_ID, d" < countries-albers.ndjson > countries-albers-id.ndjson
```

## Tools
[Back to Top](#mapping-research)

* [shapefile](https://github.com/mbostock/shapefile)
* [d3-geo-projection](https://github.com/d3/d3-geo-projection)
* [d3-scale-chromatic](https://github.com/d3/d3-scale-chromatic)
* [ndjson-cli](https://github.com/mbostock/ndjson-cli)
* [topojson](https://github.com/topojson/topojson)

## Resources
[Back to Top](#mapping-research)

* [NaturalEarth](https://www.naturalearthdata.com/downloads/)
* [Census Bureau - Geography Program](https://www.census.gov/programs-surveys/geography.html)
* [TopoJSON - World Atlas](https://github.com/topojson/world-atlas)
* [TopoJSON - US Atlas](https://github.com/topojson/us-atlas)
* [D3 State Plane](https://github.com/veltman/d3-stateplane)
* [Spatial Reference](https://spatialreference.org/)
* [ColorBrewer](http://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3)

## References
[Back to Top](#mapping-research)

**Definitions**  

* [Geographic Information Systems](https://en.wikipedia.org/wiki/Geographic_information_system)
* [FIPS Codes](https://en.wikipedia.org/wiki/Federal_Information_Processing_Standard_state_code)
* [State and County FIPS Codes](https://transition.fcc.gov/oet/info/maps/census/fips/fips.txt)
* [NaturalEarth Wiki](https://en.wikipedia.org/wiki/Natural_Earth)
* [Choropleth Map](https://en.wikipedia.org/wiki/Choropleth_map)
* [Steradian](https://en.wikipedia.org/wiki/Steradian)

**Documentation**  

* [GeoJSON](https://geojson.org/)
* [GeoJSON IETF RFC7946](https://tools.ietf.org/html/rfc7946)
    * [Feature Properties](https://tools.ietf.org/html/rfc7946#section-3.2)
* [TopoJSON](https://github.com/topojson/topojson/blob/master/README.md#api-reference)
* [ndjson - Newline Delimited JSON](http://ndjson.org)
* [d3-geo-projection docs](https://github.com/d3/d3-geo-projection/blob/master/README.md)
* [d3-interpolate](https://github.com/d3/d3-interpolate/blob/master/README.md)
* [d3-scale-chromatic](https://github.com/d3/d3-scale-chromatic/blob/master/README.md)
* [d3-scale](https://github.com/d3/d3-scale/blob/master/README.md)
    * [Sequential Scales](https://github.com/d3/d3-scale/blob/master/README.md#sequential-scales)
    * [Power Scales](https://github.com/d3/d3-scale/blob/master/README.md#power-scales)
    * [Quantile Scales](https://github.com/d3/d3-scale/blob/master/README.md#quantile-scales)
* [Census Bureau Guidance for Data Users](https://www.census.gov/programs-surveys/acs/guidance.html)

**Articles**  

* [Command Line Cartography - Part 1](https://medium.com/@mbostock/command-line-cartography-part-1-897aa8f8ca2c)
* [Command Line Cartography - Part 2](https://medium.com/@mbostock/command-line-cartography-part-2-c3a82c5c0f3)
* [Command Line Cartography - Part 3](https://medium.com/@mbostock/command-line-cartography-part-3-1158e4c55a1e)
* [Command Line Cartography - Part 4](https://medium.com/@mbostock/command-line-cartography-part-4-82d0d26df0cf)
* [Why Use Make?](https://bost.ocks.org/mike/make/)
* [Line Simplification](https://bost.ocks.org/mike/simplify/)
* [How to Infer Topology](https://bost.ocks.org/mike/topology/)
* [Literate Jenks Natural Breaks and How The Idea of Code is Lost](https://macwright.org/2013/02/18/literate-jenks.html)
* [Threshold Key](https://bl.ocks.org/mbostock/4573883)
* [geo2svg - California](https://bl.ocks.org/mbostock/fb6c1e5ff700f9713a9dc2f0fd392c35)
* [Converting EPSG to D3 Projections](https://stackoverflow.com/questions/42259132/converting-epsg-projection-bounds-to-a-d3-js-map/42267008#42267008)
* [Minify TopoJSON](https://observablehq.com/@lemonnish/minify-topojson-in-the-browser)
