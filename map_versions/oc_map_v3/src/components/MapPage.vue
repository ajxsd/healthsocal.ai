<template>
    <div>
      <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
      <!--Creates 5 maps that each represent a different metric, can add more as needed-->
      <input type="radio" id="map1" value="map1" v-model="selectedMap" @change="updateMap">
      <i class="fa fa-gears"></i>
      <label for="map1">  MHI     </label>
      <input type="radio" id="map2" value="map2" v-model="selectedMap" @change="updateMap">
      <i class="fa fa-cutlery"></i>
      <label for="map2">  Food      </label>
      <input type="radio" id="map3" value="map3" v-model="selectedMap" @change="updateMap" icon="dollar">
      <i class="fa fa-dollar"></i>
      <label for="map3">  Economic Stability      </label>
      <input type="radio" id="map4" value="map4" v-model="selectedMap" @change="updateMap">
      <i class="fa fa-group"></i>
      <label for="map4">  Community and Social Context Stability      </label>
      <input type="radio" id="map5" value="map5" v-model="selectedMap" @change="updateMap">
      <i class="fa fa-pencil"></i>
      <label for="map5">  Education</label>
      <!--If new map is made, copy l-map and l-tile-layers, but other layers will be different depending on data file used-->
      <div v-if="selectedMap === 'map1'" id="app">
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
          <!--data links to csv/js data file, titleKey and idKey identify specific column of data
              value links to specific data value/metric in the data file, geojsonIdKey identifies geojson file
              for choropleth layer, geojson is file name itself-->
          <l-choropleth-layer :data="weighted_mhi_data" titleKey="zcta" idKey="zcta" :value="ocValue" geojsonIdKey="dpto" :geojson="ocGeojson" :colorScale="colorScale" :on-hover="changeDisplay">
            <template slot-scope="info">
                <!--info control is the pop up box on the bottom left; item is the numerical data value of the
                    current area that is being hovered over, unit is the metric of the data value, for example
                    MHI map displays Mental Health Index, Economic Map is in percentages, etc.
                    title is the light grey text that appears at the top indicating the title,
                    placeholder is placeholder text that appears when not hovering over an area-->
                <l-info-control :item="info.currentItem" :unit="info.unit" title="Zip Code" placeholder="Hover over a zip code"/>
                <!--reference chart is the box at the top right of the page, showing the map's explanation and scale
                    title is the description/title of the current map
                    colorscale is the scale of colors, by default a green-yellow-red scale
                    min and max can be copied as they are shown to have the program set
                    default values for the min and max, which will be the min and max found in the
                    data respectively-->
              <l-reference-chart title="Mental Health Index Averages by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
            </template>
          </l-choropleth-layer>
        </l-map>
      </div>
    <div>
      <div v-if="selectedMap === 'map2'">
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
          <l-choropleth-layer :data="weighted_sdoh_data" titleKey="zcta" idKey="zcta" :value="wfoodValue" geojsonIdKey="dpto" :geojson="ocGeojson" :colorScale="colorScale">
            <template slot-scope="info">
              <l-info-control :item="info.currentItem" :unit="info.unit" title="Zip Code" placeholder="Hover over a zip code"/>
              <l-reference-chart title="Average Percentage of Households not recieving Food Stamps by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
            </template>
          </l-choropleth-layer>
        </l-map>
      </div>
    </div>
    <div>
      <div v-if="selectedMap === 'map3'">
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
          <l-choropleth-layer :data="weighted_sdoh_data" titleKey="zcta" idKey="zcta" :value="weconValue" geojsonIdKey="dpto" :geojson="ocGeojson" :colorScale="colorScale">
            <template slot-scope="info">
              <l-info-control :item="info.currentItem" :unit="info.unit" title="Zip Code" placeholder="Hover over a zip code"/>
              <l-reference-chart title="Average Percentage of Unemployment (Ages 16+) by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
            </template>
          </l-choropleth-layer>
        </l-map>
      </div>
    </div>
    <div>
      <div v-if="selectedMap === 'map4'">
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
          <l-choropleth-layer :data="weighted_sdoh_data" titleKey="zcta" idKey="zcta" :value="wcomValue" geojsonIdKey="dpto" :geojson="ocGeojson" :colorScale="colorScale">
            <template slot-scope="info">
              <l-info-control :item="info.currentItem" :unit="info.unit" title="Zip Code" placeholder="Hover over a zip code"/>
              <l-reference-chart title="Average Distance to the nearest Health Clinic by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
            </template>
          </l-choropleth-layer>
        </l-map>
      </div>
    </div>
    <div>
      <div v-if="selectedMap === 'map5'">
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
          <l-choropleth-layer :data="weighted_sdoh_data" titleKey="zcta" idKey="zcta" :value="weduValue" geojsonIdKey="dpto" :geojson="ocGeojson" :colorScale="colorScale">
            <template slot-scope="info">
              <l-info-control :item="info.currentItem" :unit="info.unit" title="Zip Code" placeholder="Hover over a zip code"/>
              <l-reference-chart title="Average Percent of People (Ages 16-19) Unemployed and Not in School by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
            </template>
          </l-choropleth-layer>
        </l-map>
      </div>
    </div>
  </div>
  </template>
  
  <script>
  /* must import vue components, as well as any data and geojson files needed*/

  import { InfoControl, ReferenceChart, ChoroplethLayer } from 'vue-choropleth'
  //import { geojson } from './data/py-departments-geojson'
  import { sdohData } from '../data/sdohData'
  import ocGeojson from '../data/ocGeoZipCode.json'
  import { ocMHData } from '../data/avg_data_zipcode'
  import { weighted_mhi_data } from '../data/weighted_mhi_data'
  import { weighted_sdoh_data } from '../data/weighted_sdoh_data'
  import {LMap, LTileLayer} from 'vue2-leaflet';
  export default {
    name: "app",
    components: { 
      LMap,
      LTileLayer,
      'l-info-control': InfoControl, 
      'l-reference-chart': ReferenceChart, 
      'l-choropleth-layer': ChoroplethLayer,
    },
    data() {
        return {
          /* assign values to all variables that are used above in the map parts, if using OpenStreetMap,
             copy the values for url and attribution to new map */
          url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
          attribution: '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
          ocMHData,
          dollar: 'C:\Users\erick\Documents\GitHub\healthsocal.ai\map_versions\oc_map_v3\src\assets\logo.png',
          ocGeojson,
          sdohData,
          weighted_sdoh_data,
          weighted_mhi_data,
          selectedMap: 'map1',
          colorScale: ['#fee8c8','#fdbb84','#e34a33'],
          /* each map has its own value that they represent, if making a new map, create a new
             variable as shown here and assign it a key (the data you want to display from the data file),
             and a metric to represent the data in */
        foodValue: {
          key: "ACS_PCT_HH_NO_FD_STMP_BLW_POV_ZC",
          metric: "Food Stamps"
        },
        ocValue: {
          key: "w_mhi_avg",
          metric: "Weighted Mental Health Index Average"
        },
        econValue:{
          key: "ACS_PCT_UNEMPLOY_ZC",
          metric: "% Unemployed"
        },
        comValue: {
          key: "POS_DIST_CLINIC_ZP",
          metric: "Distance Nearest Health Clinic"
        },
        eduValue: {
          key: "ACS_TOT_POP_WT_ZC",
          metric: "% Teens and Adults Unemployed and Not in School"
        },
        wfoodValue: {
          key: "w_no_food_stamps",
          metric: "Food Stamps"
        },
        weconValue: {
          key: "w_umemployed",
          metric: "% Unemployed"
        },
        wcomValue: {
          key: "w_distance_clinic",
          metric: "Distance Nearest Clinic"
        },
        weduValue: {
          key: "w_no_school_job",
          metric: "Number Teens and Adults Unemployed and Not in School"
        },
          /* if using OpenStreetMap, leave as true to give credit */
          mapOptions: {
            attributionControl: true
          },
          currentStrokeColor: '3d3213'
        }
      
    },
    methods: {
       
    }
  }
  </script>
  <style>
  body {
    background-color: #e7d090;
    margin-left: 0px;
    margin-right: 0px;
  }
  #map {
    background-color: rgb(0, 0, 0);
  }
  .info-control-wrapper {
    background-color: blueviolet;
  }
  </style>