<template>
    <div>
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"/>
          <l-choropleth-layer v-for="layer in layers" :key="layer.id" :data="layer.data" :titleKey="layer.titleKey" :idKey="layer.idKey" :value="layer.value" 
                                                      :geojsonIdKey="layer.geojsonIdKey" :geojson="layer.geojson" :colorScale="layer.colorScale" :visible="layer.visible"/>
        </l-map>
        <div v-for="layer in layers" :key="layer.id">
          <label>
            <input type='checkbox' v-model="layer.visible" v-on:click='toggleMap(layer)'/>
            {{ layer.name }}
          </label>
        </div>
        <!--<template slot-scope="info">
            <l-info-control :item="info.currentItem" :unit="info.unit" title="Zip Code" placeholder="Hover over a zip code"/>
            <l-reference-chart title="Mental Health Index Averages by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
          </template>
      <div v-for="layer in layers" :key="layer.id">
        <label>
          <input type='checkbox' v-model="layer.visible" v-on:change="layer.visible">
          {{ layer.name }}
        </label>
      </div>-->
    </div>
            <!--<l-info-control :item="info.currentItem" :unit="info.unit" title="Zip Code" placeholder="Hover over a zip code"/>
            <l-reference-chart title="Social Determinants of Health by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>-->
</template>

<script>
import { ChoroplethLayer } from 'vue-choropleth'

//import { geojson } from './data/py-departments-geojson'
import { sdohData } from './data/sdohData'
import ocGeojson from './data/ocGeoZipCode.json'
import { ocMHData } from './data/avg_data_zipcode'
import {LMap, LTileLayer} from 'vue2-leaflet';

export default {
  name: "app",
  components: { 
    LMap,
    LTileLayer,
    //'l-info-control': InfoControl, 
    //'l-reference-chart': ReferenceChart, 
    'l-choropleth-layer': ChoroplethLayer,
  },
  data() {
      return {
        url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
        attribution: '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
        layers: [
          { id: 1, idKey: "zip_code", name: 'MHI', titleKey:'zip', data: ocMHData, colorScale: ["71ae46", "ebe12a", "ac2026"], 
            value: { key: "mhi_avg", metric: "Mental Health Index Average"}, geojsonIdKey:"dpto", geojson: ocGeojson, currentStrokeColor: '3d3213', visible: true
          },
          { id: 2, idKey: "ZCTA", name: 'SDOH', titleKey:"ZIPCODE", data: sdohData, colorScale: ["71ae46", "ebe12a", "ac2026"], 
            value: { key: "ACS_PCT_HH_NO_FD_STMP_BLW_POV_ZC", metric: "Food Stamps"}, geojsonIdKey:"dpto", geojson: ocGeojson, currentStrokeColor: '3d3213', visible: false
          }
        ],
        mapOptions: {
              attributionControl: true
            }
      }

  },
  methods: {
    toggleMap(layer){
      console.log(layer.id)
      console.log(layer.visible)
      layer.visible = !layer.visible
      console.log(layer.visible)
    }
  }
}

</script>
<style>
@import "../node_modules/leaflet/dist/leaflet.css";
body {
  background-color: #e7d090;
  margin-left: 0px;
  margin-right: 0px;
}

#map {
  background-color: rgb(0, 0, 0);
}
</style>
