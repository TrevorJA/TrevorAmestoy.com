---
title: 'Interactive Geo-Spatial Maps with Folium for Python'
date: 2023-04-05
permalink: /posts/2023/Folium/
tags:
  - Folium
  - Python
  - Maps
---

Interactive mapping and data visualization provide data scientists and researchers with a unique opportunity to explore and analyze spatial data, and to share their work with stakeholders in a more engaging and accessible way. 

Personally, I've found that constructing an interactive map for my own research, and iteratively updating it as the work progresses, has been great for improving my own understanding of my project.

I've had a lot of success using the [`folium`](https://python-visualization.github.io/folium/#:~:text=map%20via%20folium%20.-,Concepts,as%20markers%20on%20the%20map.) Python package to create interactive maps, and want to share the tool with you all here. 

In this post, I provide a demonstration on how to use the `folium` package to create an HTML based interactive map of a hydrologic watershed.

![Comparing interactive vs static maps](./images/map_comparison.png)


## Folium: a quick introduction

[Folium](https://python-visualization.github.io/folium/#:~:text=map%20via%20folium%20.-,Concepts,as%20markers%20on%20the%20map.) is a Python package that is used to create interactive maps. 

It is built on top of the [Leaflet JavaScript library](https://leafletjs.com/) and can be used to visualize geospatial data in unique ways. Using `folium`, users construct maps by adding various features including lines, markers, pop-up text boxes, and different base-maps (aka, tiles), and can export maps as interactive HTML documents.

The full Folium documentation is [here](https://python-visualization.github.io/folium/), however I think it is best to learn through an example.

## Demo: Mapping a hydrologic basin data with Folium

All of the code and data used in this post is located in a GitHub repository: [trevorja/Folium_Interactive_Map_Demo.](https://github.com/TrevorJA/Folium_Interactive_Map_Demo)

To interact with the demo map directly, download the file here: [`basin_map.html`](https://github.com/TrevorJA/Folium_Interactive_Map_Demo/blob/main/basin_map.html)

Here is a brief video showcasing the interaction with the final map:

![GIF showing interactive map](./images/interactive_map_usage.gif)


The [`folium_map_demo.ipynb`](https://github.com/TrevorJA/Folium_Interactive_Map_Demo/blob/main/folium_map_demo.ipynb) walks through the process shown below and will re-create the map shown above. You can create a similar plot for a different basin by changing the `station_id` number inside ['retrieve_basin_data.ipynb'](https://github.com/TrevorJA/Folium_Interactive_Map_Demo/blob/main/retrieve_basin_data.ipynb) to a specific USGS gauge of interest. 

### Map Data

For this demo, I am plotting various features within a hydrologic basin in northern New Mexico. 

All of the data in the map is retrieved using the [`pynhd`](https://github.com/hyriver/pynhd) package from the [HyRiver](https://hyriver.readthedocs.io/en/latest/) suite for python. For more information about these packages, see my previous post, [*Efficient hydroclimatic data accessing with HyRiver for Python*.](https://waterprogramming.wordpress.com/2022/09/20/efficient-hydroclimatic-data-accessing-with-hyriver-for-python/)

The script ['retrieve_basin_data.ipynb'](https://github.com/TrevorJA/Folium_Interactive_Map_Demo/blob/main/retrieve_basin_data.ipynb) is set up to retrieve several features from the basin, including:
- Basin boundary
- Mainstem river
- Tributary rivers
- USGS gauge locations
- New Mexico Water Data Initiative (NMWDI) Sites
- HUC12 pour points

The geospatial data (longitude and latitudes) for each of these features are exported to [`data/basin_data.csv`](https://github.com/TrevorJA/Folium_Interactive_Map_Demo/blob/main/data/basin_data.csv) and used later to generate the folium map.

### Constructing a folium hydrologic map

Like many other data visualization programs, `folium` maps are constructed by iteratively adding features or map elements to the main map object. It is easy to add a map feature, however it takes more care to ensure that the features are well-organized and respond to user interaction the way you want them to.

In this demo, I deconstruct the process into five parts:
1. Initializing the map
2. Initializing feature groups (layers)
3.  Adding points to feature layers
4. Adding polygons & lines to feature layers
5. Adding layers onto the map

Before going any further, we need to import the necessary packages, load our basin data, and convert the geospatial data to `geopandas.GeoDataFrame()` geometry objects:

```python
# Import packages
import pandas as pd
import folium
from folium.plugins import MarkerCluster
import geopandas as gpd
from shapely.geometry import Point, LineString
from shapely import wkt

# Specify the coordinate reference system (CRS)
crs = 4386

# Load the basin data
basin_data = pd.read_csv('./data/basin_data.csv', sep = ',', index_col=0)

# Convert to a geoDataFrame and geometry objects
basin_data['geometry'] = basin_data['geometry'].apply(wkt.loads)
geodata = gpd.GeoDataFrame(basin_data, crs = crs)
```

Additionally, I start by specifying a couple design options before getting started (colors, line widths, etc.). I do this at the start so that I can easily test different colors, and I store all of these specifications in a `dict`. This step is not necessary, but just helps to stay organized. The following code block shows _some_ of these specifications, but some are left out just to make this post shorter:

```python
## Plot options
# Line widths
basin_linewidth = 2
mainstem_linewidth = 3
tributary_linewidth = 1

# ...
# More color and design specs here...
# ...

# Combine options into a dictionary
plot_options = {
    'station':{'color': usgs_color,
               'size': usgs_size},
    'pourpoint': {'color': pourpoint_color,
                  'size': pourpoint_size},
    'nmwdi': {'color': nmwdi_color,
              'size': nmwdi_size}
              }
```

With that out of the way, we will start mapping.

#### Initializing the map

We start to construct our map using the `folium.Map()` function:

```python
# Initialize the map
geomap = folium.Map(location = [36.5, -106.5],
                    zoom_start = 9.2,
                    tiles = 'cartodbpositron',
                    control_scale = True)
```

The `location` and `zoom_start` arguments set the default view; the user will be able to pan and zoom around the map, but this will be the starting location.

The `tile` argument in the initial `folium.Map()` calls sets the default base-map style, but different styles can be added using the `folium.TileLayer()`  function. Each `TileLayer()`  call adds a different base-map style that is then available from the drop-down menu in the final figure.

```python
## Add different tiles (base maps)
folium.TileLayer('openstreetmap').add_to(geomap)
folium.TileLayer('stamenwatercolor').add_to(geomap)
folium.TileLayer('stamenterrain').add_to(geomap)
```

Here is a side-by-side comparison of the four different tiles used in this demo:

![Comparison of folium tile options](./images/compare_folium_tiles.png)


#### Initializing feature groups (layers)

Before we add any lines or makers to the map, it is best to set up different layers using `folium.FeatureGroup()`. When the map is complete, the different feature groups can be toggled on-and-off using the drop-down menu.

Below, I initialize a `folium.FeatureGroup` for each of the six feature types in the map.

```python
# Start feature groups for toggle functionality
basin_layer = folium.FeatureGroup(name = 'Basin Boundary', show=True)
usgs_layer = folium.FeatureGroup(name = 'USGS Gauges', show=True)
mainstem_layer = folium.FeatureGroup(name = 'Mainstem River', show=True)
tributary_layer = folium.FeatureGroup(name='Tributary Rivers', show=False)
pourpoint_layer = folium.FeatureGroup(name= 'HUC12 Pour Points', show=False)
nmwdi_layer = folium.FeatureGroup(name='NM Water Data Initiative Gauge', show=True)
```

The `name` argument is what will be displayed on the drop-down menu. The `show` argument indicates whether that layer is visible by default (when the map is first opened), or whether it first needs to be selected using the drop-down menu.

Now that the layers are initialized, we can begin adding the features (polygons, lines, and point markers) to each layer.

#### Adding points to feature layers

The `folium.CircleMarker()` is used to add circle points using a specific coordinate location.

The following code shows how I am iteratively adding different points to the different layers of the map based upon the feature type.  

For each point, I extract the latitude and longitude (`coords = [point.geometry.y, point.geometry.x]`) and pass it to the `folium.CircleMarker()` function with colors and sizes specific to each of the three different point features (USGS gauge stations (`station`), NMWDI (`nmwdi`), and HUC12 pourpoints (`pourpoint`)):

```python
plot_types = ['station', 'nmwdi', 'pourpoint']
plot_layers = [usgs_layer, nmwdi_layer, pourpoint_layer]

# Loop through the different feature point types
for i, feature_type in enumerate(plot_types):

	# Specify the correct feature layer to add the point too
	map_layer = plot_layers[i]

	# Add each point
    for _, point in geodata.loc[geodata['type'] == feature_type].iterrows():    
        coords = [point.geometry.y, point.geometry.x]
        
        # Add the popup box with description
        textbox = folium.Popup(point.description,
                               min_width= popup_width,
                               max_width= popup_width)

		# Add the marker at the coordinates with color-coordination
        folium.CircleMarker(coords,
                            popup= textbox,
                            fill_color = plot_options[feature_type]['color'],
                            fill = True,
                            fill_opacity = fill_opacity,
                            radius= plot_options[feature_type]['size'],
                            color = plot_options[feature_type]['color']).add_to(map_layer)
```


#### Adding polygons & lines to feature layers

I've found that it can be difficult to add Polygons or Lines to a `folium` map if the coordinate geometry are not formatted correctly. For me, the best method has been to convert the polygon or line data to a `geopandas.GeoSeries` and then converting this to JSON format data using the `.to_json()` method.

Once in JSON format, the data can be added to the map using the `folium.GeoJson()` method similar to other features.  Although, rather than adding it to the map directly, we add it to the specific feature layer so that we can toggle things later.

Below, I show how I add the basin boundary polygon to the map. This needs to be repeated for the mainstem and tributary river lines, and the full code is included in [`folium_map_demo.ipynb`](https://github.com/TrevorJA/Folium_Interactive_Map_Demo/blob/main/folium_map_demo.ipynb). 

```python
## Plot basin border
for i,r in geodata.loc[geodata['type'] == ].iterrows():
	# Convert the Polygon or LineString to geoJSON format
    geo_json = gpd.GeoSeries(r['geometry']).simplify(tolerance = 0.000001).to_json()
    geo_json = folium.GeoJson(data= geo_json,
                              style_function=lambda x: {'fillcolor': 'none',
                                                        'weight': basin_linewidth,
                                                        'color': basin_color,
                                                        'opacity': 1,
                                                        'fill_opacity': 1,
                                                        'fill': False})
	# Add popup with line description
    folium.Popup(r.description,
                 min_width = popup_width,
                 max_width= popup_width).add_to(geo_json)
    
    # Add the feature to the appropriate layer
    geo_json.add_to(basin_layer)
```

And with that, the hard part is done.

#### Last step: adding layers onto map

Now, if you try to visualize the map it will be empty! This is because we have not added the feature layers to the map.  In this last step, we add each of the six feature layers to the map and also add the `folium.LayerControl()` which will allow for us to toggle the different layers on-and-off:

```python
# Add all feature layers to the map
basin_layer.add_to(geomap)
usgs_layer.add_to(geomap)
mainstem_layer.add_to(geomap)
tributary_layer.add_to(geomap)
pourpoint_layer.add_to(geomap)
nmwdi_layer.add_to(geomap)

# Add the toggle option for layers
folium.LayerControl().add_to(geomap)
```

Ready for the grand reveal? 

#### Viewing, saving, and sharing the map

Viewing your map is as easy as calling the map name at any point in the script (i.e., `geomap`), and `folium` makes it easy to save the map as an HTML using the `map.save()` function as shown here:

```python
# Save and view the map
geomap.save("basin_map.html")
geomap
```

Once you have your HTML saved, and you've taken a moment to open it on your computer and made sure that the features are situated nicely, then it comes time to share. Other users can view the maps simply by opening the HTML file on their local machine, or you can add the HTML to a website.

## Concluding Thoughts

I hope you've found some inspiration here, and find a way to incorporate interactive geospatial mapping in your project. I don't think it can be overstated how much an interactive visual such as a folium map can serve to broaden the access to your dataset or model. 

Thanks for reading!

