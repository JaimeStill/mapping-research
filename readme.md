# Mapping Research

* [Converting Shapefiles](#converting-shapefiles)
    * [Install npm Packages](#install-npm-packages)
    * [Converting to GeoJSON](#converting-to-geojson)
    * [Applying a Projection](#applying-a-projection)
    * [Generating an SVG Preview](#generating-an-svg-preview)
* [GIS and Cartography Terminology](#gis-and-cartography-terminology)
    * [Scales](#scales)
* [Links](#links)

> All command line examples are executed from the root of this repo.

## Converting Shapefiles
[Back to Top](#mapping-research)

The shapefiles contained in the `source` directory were obtained from the open source [NaturalEarth](https://www.naturalearthdata.com/downloads/) repository. It contains [1:50m cultural vectors](https://www.naturalearthdata.com/downloads/50m-cultural-vectors/) for admin 0 and admin 1 (countries and states).

### Install npm Packages
[Back to Top](#mapping-research)

```
npm install -g shapefile
npm install -g d3-geo-projection
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

## GIS and Cartography Terminology
[Back to Top](#mapping-research)

* **FIPS Code** - Federal Information Processing Standard state codes. Numeric and two-letter alphabetic codes that identify U.S. states and certain other associated areas.

### Scales
[Back to Top](#mapping-research)

* **1:110m** - Small scale. 1 cm = 1,100 km. Suitable for schematic maps of the world on a postcard or as a small locator globe.

* **1:50m** - Medium scale. 1 cm = 500 km. Suitable for making zoomed-out maps of countries and regions. Show the world on a tabloid size page.

* **1:10m** - Large scale. 1 cm = 100 km. The most detailed. Suitable for marking zoomed-in maps of countries and regions. Show the world on a large wall poster.

## Links
[Back to Top](#mapping-research)

* [Geographic Information Systems](https://en.wikipedia.org/wiki/Geographic_information_system)
* [FIPS Codes](https://en.wikipedia.org/wiki/Federal_Information_Processing_Standard_state_code)
* [GeoJSON](https://geojson.org/)
* [GeoJSON IETF RFC7946](https://tools.ietf.org/html/rfc7946)
* [NaturalEarth](https://www.naturalearthdata.com/downloads/)
* [NaturalEarth Wiki](https://en.wikipedia.org/wiki/Natural_Earth)
* [d3-geo-projection docs](https://github.com/d3/d3-geo-projection/blob/master/README.md)