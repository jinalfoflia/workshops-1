# Workshop FOSS4G
We guide through how to go from a open vector data to vector tiles and use it to create visualizations in a 5 step workshop.

The workshop will be containing introductions into vector tiles, Mapbox Studio Mapbox GL with exercises in between to work towards a data visualization as end result.

As part of the workshop we are going to create a map visualization of the causes of deaths in different regions in Tanzania.


![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535352649059_FXvJmgqlem.gif)


**Workshop steps**

1. Sourcing: We pick the data and prepare it
2. Processing: We join a tabular data set towards a spatial data set
3. Tiling: We turn the spatial data into vector tiles
4. Styling: We use Mapbox Studio to create a [choropleth](https://en.wikipedia.org/wiki/Choropleth_map) map
5. Publishing: We create a website using Mapbox GL JS for a data visualization

**Introductions**

- Mapbox Vector Tiles
- Mapbox Studio
- Mapbox GL
- Mapbox Style specification

 
 **Tools**

- [QGIS](https://qgis.org/en/site/forusers/download.html)
- tippecanoe (*optional)* 
- Mapbox Studio ([*sign up for an account*](https://www.mapbox.com/signup/)*)* 


## Why use Mapbox?

The Mapbox ecosystem is built on top of open source libraries that you can use without our platform. It gives you the power to create mapping applications and independent.

Mapbox GL is the only cross platform mapping library that’s available for the web, iOS, Android and desktop.  If you want to create a truly custom experience for your customer and user - you choose the Mapbox GL ecosystem.

Check out some data viz examples made in the past with Mapbox GL in the Gallery.
https://www.mapbox.com/gallery/

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535432390225_Gallery___Mapbox.png)

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535432535655_Gallery___Mapbox.png)

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535432588891_Gallery___Mapbox.png)



----------
## 1. Vector Data

Before you create any visualization you will start with the source data that you have available.
Sometimes you have to create it yourself, other times you have a proprietary data set or in the best case you are able to use an open data portal to source your vector data.

Open data sets:

- open data catalogs of countries, cities and states e.g. http://opendata.go.tz
- [World Bank data catalog](https://datacatalog.worldbank.org/)
- [Natural Earth](https://www.naturalearthdata.com/downloads/50m-cultural-vectors/) for cartographic data
- [OpenStreetMap](https://download.geofabrik.de/)

In this example we want to focus on comparing a data attribute across different sub units of a country (in the case of Tanzania these are “[regions](https://en.wikipedia.org/wiki/Regions_of_Tanzania)”).

### Picking a tabular data set
On the [Basic Statistics Portal](http://opendata.go.tz/) of Tanzania we can find the number of deaths and their cause per region.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535333479190_Number_and_causes_of_death_occured_-_Datasets_-_Basic_Statistics_Tanzania.png)



👉🏽  On your computer create a folder called `foss4g`. Go to  http://opendata.go.tz/dataset/number-and-causes-of-death-occured-by-region and download the CSV for “Number and causes of death 5 and above years-2013”.

Save it as `tanzania-deaths-above-5-years-2013.csv`  and open it in a spreadsheet editor.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535334355782_deaths_2013.png)


### Picking a geospatial data set
In order to visualize the cause of deaths across regions  we have to find shapes for the regions data got surveyed. From the World Bank we can find regional boundaries for Tanzania.

👉🏽  Go to  https://energydata.info/dataset/tanzania-region-district-boundary-2012 and download the zipped Shapefile (not the orange JSON file) for “Region bounday”.

As first step we are going to unzip the Shapefile and load the regions into QGIS to visualize them. If we load the regions into QGIS we can explore them.

Drag and drop the Shapefile into QGIS.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535334469266_QGIS_2_18_2_and_Tanzania_-_Region___District_Boundary__2012__-_Datasets_-_ENERGYDATA_INFO_and_Mapbox_Workshop_FOSS4G_2018__Dropbox_Paper.png)


Let’s assign a label to each boundary polygon by clicking on the layer and choosing the “Properties > Labels” panel.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535334738777_Layer_Properties_-_Regions___Labels_and_QGIS_2_18_2.png)



![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535334775349_QGIS_2_18_2.png)


### Creating a CSVT
We will later be able to load that CSV file into QGIS but first we need to create a [CSVT file](https://anitagraser.com/2011/03/07/how-to-specify-data-types-of-csv-columns-for-use-in-qgis/) that describes which data types our columns are in (number or text).

:point_right: [For this exercise we already prepared a CSVT for you, that you can copy and paste.](https://github.com/mapbox/workshops/blob/foss4g/foss4g-2018/data/tanzania-deaths-above-5-years-2013.csvt)


The easiest way to complete that is to add a new header row and fill in the types for each column and then save it as CSV again.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535335391545_deaths_2013.png)


Now delete all the other rows except the first header row and save it with the same filename but with `**.csvt**` as suffix. If you look at your csvt file in a text editor 

- make sure you have quotes wrapped around each field
- make sure your editor doesn’t save the file as `.csv` 
![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535335734051_Banners_and_Alerts_and_deaths_2013_csvt_and_how_to_create_a_csvt_file_-_Google_Search.png)



> Bonus: To take this a step further you should later also try to join the population per region to compare the absolute numbers to the total population per region. http://www.nbs.go.tz/nbstz/index.php/english/statistics-by-subject/population-and-housing-census/844-tanzania-total-population-by-district-regions-2016.


## 2. Processing

We now have explored the source data (one tabular and one spatial) and in the next step [we are going to join each row for each region to the appropriate shape of that region in QGIS.](https://www.qgistutorials.com/en/docs/performing_table_joins.html)

### Add CSV file to QGIS

From sourcing the vector data you should have both a CSV and a CSVT file with the same filename. Drop the CSV file into QGIS.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535336164131_QBJecjis48.gif)

### Join the datasets by region

Click on the Regions layer “Properties > Joins” and add a new vector join.
For both source and target choose the region as the join field.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535336308871_SrQ4oepxuu.gif)


If you open the attribute table of “Regions” now you should be able to see that we’ve joined all the deaths by cause columns to our original shape of the region.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535336455398_fi2d0mcDjQ.gif)


### Create a Choropleth in QGIS

Click on “Properties > Style” for the region layer and choose “Graduated” as symbol and the column you want to visualize (the total deaths).

If you click “Classify” QGIS will automatically build a histogram and a color distribution.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535337164991_Layer_Properties_-_Regions___Style_and_QGIS_2_18_2.png)

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535337266077_QGIS_2_18_2_and__Regions____Features_total__30__filtered__30__selected__0_and_Mapbox_Workshop_FOSS4G_2018__Dropbox_Paper_and_About_-_World_Bank_Group_-_Organizations_-_ENERGYDATA_INFO.png)



## 3. Vector Tiles

At this point we created a static choropleth map in QGIS.
In order to tell compelling stories our steps from here will show how to create the equivalent for the web to build a dynamic data visualization.


## 👨🏽‍🏫 🗣️ What are vector tiles

**What are raster tiles?**

Raster map: 
- Send one large image for viewport
- **Tiles:** Send many small square images

Advantages
  - Better performance
  - Caching and pre-computing

- See it in action: http://www.maptiler.org/google-maps-coordinates-tile-bounds-projection/
- Good introduction into quadkeys: https://msdn.microsoft.com/en-us/library/bb259689.aspx
- How tiling map images works: https://github.com/perliedman/tiled-maps/

![](https://github.com/perliedman/tiled-maps/raw/gh-pages/images/tiles.png)


### What are vector tiles?

Vector Map

- **Tiles are great!**
- Send encoded vector data (MVT)
- Cut vector data into many small tiles

Advantages

- Better user experience
- Style on client
- Smaller size
- **Create once, use everywhere!**


![](https://lh3.googleusercontent.com/ypnHEIZVaM0yEdKIq8ytZDpVqVul3Y0B_iUbJk8iXofKRtmUMzjVwEFV3kIZiNUAlXQHtm3z1uT7ufG3ngPILF1-HoRWriVlFUap8xe4hr3DnXr2PA8j24TFNjiYNJ-IexR3uiyfmwk)


**How geometries are encoded**
https://www.mapbox.com/vector-tiles/specification/#encoding-geom

**What do I need to create my own map?**

![](https://lh4.googleusercontent.com/6G7sVFVjp2JZOBqavCtbBR-3w5l4jO0WC3IIecmhlyC30z_BmSNrJGDolAEJ3LWusvzXXPO7rPGg4mi9vHihVbWi6EtlrPijYjewKsvzlDNZCVT8BJHgxRnBSFWAMgwBK-eVlORj8d0)

- Vector Tiles
- Style Definition
  - JSON document (Mapbox GL Style Specification)
- Client Library
  - Mapbox GL JS, Mapbox GL Native
  - OpenLayers 3
![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535374044803_SOTM_-_Google_Slides.png)


**Peek behind the curtains:** http://mapbox-gl-inspect.lukasmartinelli.ch/#2.66/40.63/-74.09


----------

If you are working with small data as we are dealing here you are able to directly convert this data into vector tiles using the Mapbox platform.

### Save file as GeoJSON

You can save your existing Shapefile as GeoJSON and the joined attributes will be included.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535357826430_Save_vector_layer_as____and_QGIS_2_18_2_and_geojson_epsg_-_Google_Search_and_Causes_of_Deaths_in_Tanzania.png)


### Upload the GeoJSON to Mapbox

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535338682424_sHRMyfAlXr.gif)


**What other filetypes can you upload?** https://www.mapbox.com/help/uploads/
**What is a tileset?** https://www.mapbox.com/help/define-tileset/

After your GeoJSON has been processed successfully you will be able to inspect the tileset that has been created from the GeoJSON.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535338849662_zs5BE0UvNT.gif)


### Alternative: Turn the GeoJSON into vector tiles using tippecanoe

The open source tool tippecanoe can turn GeoJSON features into vector tiles. Using tippecanoe you have much more control how features are encoded into tiles.

The [cook book](https://github.com/mapbox/tippecanoe#cookbook) specifies many different command line arguments for different use cases.

We can turn the GeoJSON we saved earlier directly into vector tiles.
👉🏽 [Install tippecanoe](https://github.com/mapbox/tippecanoe)


```bash
tippecanoe -zg -o tanzania.mbtiles \
  --buffer 128 \
  --drop-densest-as-needed \
  --extend-zooms-if-still-dropping $HOME/Documents/tanzania_deaths.geojson
```

An MBTiles file can be uploaded to Mapbox or you could also serve it on your own using a tileserver like https://github.com/klokantech/tileserver-gl.

**What does tippecanoe do?**
Tippecanoe is the open source tool that does the same thing when you upload a Shapefile or GeoJSON to Mapbox.com. It takes your GIS data and tries to turn it into tiles. Because locally we have full control over how we want to do that, it’s usually worth tiling the data yourself and tweaking the command line arguments.

Examples using tippecanoe: https://github.com/mapbox/tippecanoe/blob/master/MADE_WITH.md

# Visualising your data

Here are a few resources you'll need before getting started:

- Mapbox account: Sign up for free at [Mapbox](https://www.mapbox.com/signup/)
- Quick intro to Mapbox Studio: While we’ll cover some topics in this workshop, [here’s a glimpse of how Mapbox Studio is structured.](https://www.mapbox.com/help/studio-manual/)

## **Cartogram:** Custom maps in a minute!

[Cartogram](https://mapbox.com/cartogram/)

- Choose any image that will act as a reference to the style.
- Upload the image in the viewer to get started.
- Move the colour detectors around to choose the colours you want to customise the map with.
- and viola, you have a beautiful custom map! 🙂
- Add that style to Mapbox Studio to customise it further more!


![](https://d2mxuefqeaa7sj.cloudfront.net/s_5B86687B3D49AE1C0BA2F755830EE244397F5CEDEFFBBC1854E54252605FBB69_1535425773788_37541196-a778511a-297f-11e8-84ab-179905496615.gif)




![](https://d2mxuefqeaa7sj.cloudfront.net/s_5B86687B3D49AE1C0BA2F755830EE244397F5CEDEFFBBC1854E54252605FBB69_1535426474729_FOSSG42018logo+1.svg)



## 👨🏽‍🏫 Mapbox Studio- Customize details of a map style

**Mapbox Studio** is a web application for creating custom maps. You can manage and create datasets, tilesets, and map styles. To get started with **Mapbox Studio**, check out the [**Mapbox Studio**](https://www.mapbox.com/studio-manual/overview/) [manual.](https://www.mapbox.com/studio-manual/overview/)

References: 

- https://www.mapbox.com/help/choropleth-studio-gl-pt-1/
- https://www.mapbox.com/help/choropleth-studio-gl-pt-2/

Let’s get started:


- Choose one of the available styles in Mapbox Studio - let’s go with `Light`
- Click on `Add layer` and choose the tileset that you have uploaded from your previous step
![](https://d2mxuefqeaa7sj.cloudfront.net/s_5B86687B3D49AE1C0BA2F755830EE244397F5CEDEFFBBC1854E54252605FBB69_1535430792072_image.png)

- Click `Go to data`, and the map view will refocus on Dar es Salaam
- Your new layer will be highlighted on the x-ray map.
- Choose type to be `fill`
![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1534825027145_Basic_Template___Mapbox_and_foss4g2018.png)

- Click the **Style** tab and the map will switch back to style mode displaying your new layer. You will see the state data on the map with a default style (black with 100% opacity).
- You can rename a layer by clicking on the name of the layer at the top of the panel. Rename your new layer `Tanzania data`

## Styling the layer

**Data-driven styling:** In the Mapbox Studio style editor, you can assign a color to each state based on an attribute. Click the **Style** link in the `Tanzania data` layer. Next, click **Style across data range** and choose `deaths_2013_burns`


![](https://d2mxuefqeaa7sj.cloudfront.net/s_5B86687B3D49AE1C0BA2F755830EE244397F5CEDEFFBBC1854E54252605FBB69_1535431699893_image.png)

- Click on `style with data conditions` and choose `deaths_2013_burns` number.
- The rate of change is set to *linear*. Click `Edit` and select `Step` instead. Click `Done`. Since you have set the rate of change to step, the colors for each range of density between stops will be uniform.
![](https://d2mxuefqeaa7sj.cloudfront.net/s_5B86687B3D49AE1C0BA2F755830EE244397F5CEDEFFBBC1854E54252605FBB69_1535432386004_image.png)

- Now it’s time to start adding stops and colors! You will create several stops to break states up into groups with similar death rates. Click on **Edit** in the first density stop. Click on it and change the color to `#FFEDA0`. Click **Done**.
- Click **+ Add another stop**. Change *density* to `2`, and change the color to `#FED976`. Click **Done**.
- Create the following additional stops:
    - 4: #d39e0d
    - 6: #FD8D3C
    - 8: #FC4E2A
    - 10: #E31A1C
    - 12: #BD0026
    - 14: #7a0018


![](https://d2mxuefqeaa7sj.cloudfront.net/s_5B86687B3D49AE1C0BA2F755830EE244397F5CEDEFFBBC1854E54252605FBB69_1535433143543_image.png)



- Click on publish! 


![](https://d2mxuefqeaa7sj.cloudfront.net/s_5B86687B3D49AE1C0BA2F755830EE244397F5CEDEFFBBC1854E54252605FBB69_1535433317384_image.png)

## Interactivity

In the next step we want to take our static map

- allow filtering the visualization by cause of death 
- add a popup to it to show more information about the deaths of each region
![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1534933904513_Causes_of_Deaths_in_Tanzania.png)


### Introduction 👨🏽‍🏫 to Mapbox GL

- How web maps work: https://www.mapbox.com/help/how-map-design-works/
- The Mapbox GL JS SDK: https://www.mapbox.com/mapbox-gl-js/api/
- Style specification (CSS for maps): https://www.mapbox.com/mapbox-gl-js/style-spec
- Dynamic functions for data driven styling: https://www.mapbox.com/help/mapbox-gl-js-expressions/
 

## The new era of interactive cartography

Features:

- cross platform
- zoom and rotation
- fly animations
- weaving in new layers
- data driven styling
- any object can be interactive
- because of a declarative styling language you can use a map designer (Mapbox studio) 
- less bandwidth
- label collision on the client
- 87% of global internet browser support web gl, 95% in the US

Under the hood:
- Mapbox GL draws your geometries using many triangles (it's triangles all the way down)
  - vertex shaders (transforms coordinates)
  - fragment shaders (transforms colors)
  - drawing fonts
- Because of so many fundamental problems we have to solve there are some great libraries created as part of the process
  - triangulation: https://github.com/mapbox/earcut
  - 2d indexing: https://github.com/mourner/rbush
  - packing many small rectangles into a big rectangle: https://github.com/mapbox/shelf-pack
  - slice geojsons into vector tiles on the fly: https://github.com/mapbox/geojson-vt

### Display a map

As first step we create a HTML page that displays the map we made in Mapbox Studio. You will need to copy your [style id](https://www.mapbox.com/help/define-style-id/) and access token from Mapbox studio.

👉🏽 Follow along https://www.mapbox.com/mapbox-gl-js/example/simple-map/

We create a new file `index.html` with a HTML skeleton.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset='utf-8' />
  <title>Causes of Deaths in Tanzania</title>
  <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
  <script src='https://api.tiles.mapbox.com/mapbox-gl-js/v0.46.0/mapbox-gl.js'></script>
  <link href='https://api.tiles.mapbox.com/mapbox-gl-js/v0.46.0/mapbox-gl.css' rel='stylesheet' />
  <link href="https://api.mapbox.com/mapbox-assembly/v0.23.0/assembly.min.css" rel="stylesheet">
  <script async defer src="https://api.mapbox.com/mapbox-assembly/v0.23.0/assembly.js"></script>
</head>
<body>
  <div class='flex-parent viewport-full relative clip'>
    <!-- insert sidebar here -->
    <div id='map' class='flex-child flex-child--grow bg-darken10 viewport-twothirds viewport-full-ml'>
      <!-- this div will be initialized with a WebGL
      context where the map is rendered -->
    </div>
  </div>
  <!-- insert script and style snippets here -->
</body>
</html>
```

On top of Mapbox GL we also add CSS framework called [Assembly](https://www.mapbox.com/assembly/) we use at Mapbox to get started quickly.

Now we need to initialize and add a map to the web page. Add a new script element before the `</body>` tag ends and initialize the map.


```html
<script>
// your access token
mapboxgl.accessToken = 'pk.eyXXX';
var map = new mapboxgl.Map({
  container: 'map',
  // your style id
  style: 'mapbox://styles/yourusername/styleid',
  zoom: 6,
  // initialize the map to center on Tanzania
  center: [35.170672, -8.459891],
  hash:true
});
</script>
```

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535345677602_Causes_of_Deaths_in_Tanzania.png)


### Add a popup
As we hover over a region we want to list all the different counts of deaths for that region.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535434469610_Causes_of_Deaths_in_Tanzania_and_1__lukasmartinelli_Lukass-MacBook-Pro____Documents_mapbox_workshops_foss4g-2018_viz__zsh__and_Slack_-_Mapbox.png)


First we add some CSS rules to the `<style>` tag.

```html
<style>
.mapboxgl-popup-content b {
  display: inline-block;
  width: 9em;
}
.mapboxgl-popup-content span {
  display: inline-block;
  min-width: 4em;
  text-align: right;
}
</style>
```

In a second step we set up a listener that whenever the mouse moves over a map element, we query the features the mouse is hovering over and populating a popup with the necessary information for that feature (in our case the region).

Add this function inside before your `</script>` tag ends.


```js
function setupPopupHandler() {
  var popup = new mapboxgl.Popup({
      closeButton: false,
      closeOnClick: false
  });

  map.on('mousemove', function(e) {
    var features = map.queryRenderedFeatures(e.point, {layers: ['tanzania_deaths']});
    if(!features.length) {
      map.getCanvas().style.cursor = '';
      popup.remove();
    } else if (features.length > 0) {
      map.getCanvas().style.cursor = 'pointer';    
      var hoveredFeature = features[0];
      var props = hoveredFeature.properties;
      var popupHtml = "<b>Diabetes Mellitus:</b>"
        + "<span>" + props['deaths_2013_Diabetes Mellitus'] + "</span><br />"
        + "<b>Malaria severe:</b>"
        + "<span>" + props['deaths_2013_Malaria- Severe, Complicated'] + "</span>"
        + "<hr class='txt-hr my3'>"
        + "<b>Total Deaths:</b>"
        + "<span>" + props['deaths_2013_Total'] + "</span>";
      
      popup.setLngLat(e.lngLat)
           .setHTML(popupHtml)
           .addTo(map);
    }
  });
}
```
    

Now that we have set up the initialization code, we add a function call after the map has loaded.

```js
map.on('load', function () {
  // add zoom and rotation controls to the map.
  map.addControl(new mapboxgl.NavigationControl());
  setupPopupHandler();
});
```

### Change attribute to interpolate

In Mapbox Studio we used the design editor to create our map. Under the hood
Mapbox Studio creates a JSON file describing how the map data should be rendered (the GL style document).

The code to describe the choropleth layer.

```js
{
  "id": "tanzania_deaths",
  "type": "fill",
  "source": "composite",
  "source-layer": "tanzadeaths-9dbsr2",
  "paint": {
    "fill-color": [
      "interpolate",
      ["linear"],
      ["get", "deaths_2013_Total"],
      301, "#fee8c8",
      1196, "#fdbb84",
      2090, "#e34a33"
    ]
  }
}
```

Let’s decompose this. The `fill` type describes that we want to color a polygon and the `fill-color` paint property describes a [interpolate expression](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-interpolate). You can think of this expression as a small function that interpolates colors between several different color stops/data values.
 
 With `["get", "deaths_2013_Total"]` we describe the field we want to use to interpolate the color across.
And with `301, "#fee8c8", 1196, "#fdbb84", 2090, "#e34a33"` we define our color range. Meaning from the value `301` we start with the color `#fee8c8` and interpolate linearly until at 1196 we interpolate it towards `#fdbb84` after which until 2090 we interpolate towards `#e34a33`.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535346867236_Basic_Template___Mapbox.png)


 
If we want to switch out the attribute to interpolate across we have to change two arguments:

- the name of the field
- provide the data range for interpolation

Let’s add a new function `colorRegions` to our `<script>` tag. In this function we are changing the `fill-color` of the visualization layer in our style to be dependent on a field we pass in.

Because the data distribution is different for each field we also have to pass in the color stops we want to interpolate across.

```js
function colorRegions(interpolationField, stops) {
  if (stops.length < 3) throw new Error('Need to provide at least 3 stops to interpolate across');
  map.setPaintProperty('tanzania_deaths', 'fill-color', [
    "interpolate",
    ["linear"],
    ["get", interpolationField],
    stops[0], "#fee8c8",
    stops[1], "#fdbb84",
    stops[2], "#e34a33"
  ]);
}
```

We will be calling this function each time the user changes the cause of death on the left.
To make it more maintainable we are maintaining all the data ranges in a separate variable `dataRanges`.

Add the `dataRanges` variable at the top of your script and call the `colorRegions` function at least once in the load event to test.


```js
var dataRanges = {
  'deaths_2013_Total': [301, 1196, 2090],
  'deaths_2013_Diabetes Mellitus': [1, 33, 65]
};

map.on('load', function () {
  // add zoom and rotation controls to the map.
  map.addControl(new mapboxgl.NavigationControl());
  setupPopupHandler();
  colorRegions('deaths_2013_Diabetes Mellitus', dataRanges['deaths_2013_Diabetes Mellitus'])
});
```

![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535347505890_6dLNo21Dl9.gif)


### Add sidebar

Below is code for a sidebar that allows choosing a different attribute to color the map by. Insert the `div` with the id `sidebar` where the `<!-- insert sidebar here -->` in the placeholder is.

In the sidebar we are setting up a radio button for each attribute `<input type="radio" name="radio-filter" value="deaths_2013_Malaria- Severe, Complicated">` that contains the field name in the layer as value.
When you click on that radio button, we are going to set up a handler that will change the style to interpolate the map according to that field.

```html
<div class='flex-parent viewport-full relative clip'>
  <!-- insert sidebar here -->
  <div id="sidebar" class="flex-child w-full w240-ml absolute static-ml left bottom">
    <div class="flex-parent flex-parent--column viewport-third h-full-ml hmax-full">
      <div class="flex-child flex-child--grow py12 px12 scroll-auto">
        <h2 class="txt-xl txt-bold mb6">Deaths in Tanzania</h2>
        <p class="txt-s"><strong class="txt-bold">Number and causes of death occured</strong> (2013) <br><strong class="txt-bold">Source:</strong> <a class="link link--blue-light" target="_blank" href="http://opendata.go.tz/dataset/number-and-causes-of-death-occured-by-region">http://opendata.go.tz/</a></p>
        <div id="filter-ui" class="mt12">
          <h4 class="txt-m txt-bold mb6">Cause of death</h4>
            <label class="radio-container">
            <input type="radio" name="radio-filter" value="deaths_2013_Total" checked="">
            <div class="radio mr6"> </div>
            <span>Total</span>
          </label><br>
          <label class="radio-container">
            <input type="radio" name="radio-filter" value="deaths_2013_Malaria- Severe, Complicated">
            <div class="radio mr6"> </div>
            <span>Malaria</span>
          </label><br>
          <label class="radio-container">
            <input type="radio" name="radio-filter" value="deaths_2013_Diabetes Mellitus">
            <div class="radio mr6"> </div>
            <span>Diabetes Mellitus</span>
          </label><br>
        </div>
      </div>
    </div>
  </div>
  <div id='map' class='flex-child flex-child--grow bg-darken10 viewport-twothirds viewport-full-ml'></div>
</div>
```

In `setupFilters` we query all the radio buttons and attaching a click handler to it.
Once activated we recolor the regions in the map using `colorRegions` with the field chosen and the specific stops for that field.

```javascript
function setupFilters() {
  var filterContainer = document.getElementById('filter-ui');
  var filterList = filterContainer.getElementsByTagName('input');

  for (var i = 0; i< filterList.length; i++) {
    filterList[i].onclick = function(e) {
      var field = e.target.value;
      var stops = dataRanges[field];
      colorRegions(field, stops);
    };
  }
}
```

And after the map is loaded we want to set up our filtering code.

```javascript
map.on('load', function () {
  // add zoom and rotation controls to the map.
  map.addControl(new mapboxgl.NavigationControl());
  setupPopupHandler();
  setupFilters();
});
```


![](https://d2mxuefqeaa7sj.cloudfront.net/s_6552AB0028AE719237B17030D756B0987E7502EBDA68BD604E8071746D1363EF_1535347901595_aHM7hCF5Dz.gif)


## Summary

In this workshop you have learned what Mapbox GL is and how you can leverage it together with vector tiles to create your own data visualizations.
You can find many more resources on our documentation or examples page.

- 📖 https://www.mapbox.com/help/how-web-apps-work/
- 📖 https://www.mapbox.com/mapbox-gl-js/api/
- 📖 https://www.mapbox.com/mapbox-gl-js/examples/

To learn more about Mapbox GL join the talk of Konstantin about “Interactive mobile maps with the Mapbox Maps SDK” at 11:30am on Friday at Bagamoyo.
[And keep in touch with us during FOSS4G.](https://www.mapbox.com/events/foss4g-global-2018/)
