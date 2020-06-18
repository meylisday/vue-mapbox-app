<template>
  <div id="map">
    <Mapbox
      :access-token="accessToken"
      :map-options="{
        style: mapStyle,
        center: [151.209152, -33.875305],
        zoom: 15,
      }"
      :geolocate-control="{
      show: true, 
      position: 'top-right'
    }"
      :scale-control="{
      show: true,
      position: 'top-right'
    }"
      :fullscreen-control="{
      show: true,
      position: 'top-right'
    }"
      @map-load="loaded"
      @map-click:points="clicked"
      @map-mouseenter:points="mouseEntered"
      @map-mouseleave:points="mouseLeft"
    />
  </div>
</template>

<script>
const Mapbox = () => import("mapbox-gl-vue");
import { Popup } from "mapbox-gl";
import PopupContent from "./PopupContent.vue";
import { EventBus } from "../event-bus.js";

export default {
  name: "Map",
  props: ["filters", "search"],
  components: {
    Mapbox
  },
  data() {
    return {
      accessToken:
        "pk.eyJ1IjoibWV5bGlzZGF5IiwiYSI6ImNrYmZjcnVoYTBhaHQycm16Y2p5cnBkZGMifQ.uoHz831uX0nZFagLbYwXbw", // your access token. Needed if you using Mapbox maps
      mapStyle: "mapbox://styles/mapbox/light-v9",
      source: {
        type: "geojson",
        data:
          "https://bitbucket.org/idda/coding-challenges/raw/88be221f75a1b108c9e5f7222906b2735c147ac8/testBlob.json"
      },
      options: {
        id: "points",
        interactive: true,
        type: "symbol",
        source: "state-data",
        // 'source-layer': 'state-data',
        layout: {
          "icon-image": "circle-11",
          // "text-field": ["get", "Title", ["get", "project"]],
          "text-font": ["Open Sans Semibold", "Arial Unicode MS Bold"],
          "text-offset": [0, 0.6],
          "text-anchor": "top",
          "icon-allow-overlap": true,
          "text-allow-overlap": true
        }
      }
    };
  },
  watch: {
    filters: function() {
      this.renderLayer(this.getFilters());
    },
    search: function() {
      this.renderLayer(this.getFilters());
    }
  },
  mounted() {
    EventBus.$on("zoom", this.calculateCenter.bind(this));
  },
  methods: {
    loaded(map) {
      this.map = map;
      this.map.addSource("state-data", this.source);
      this.renderLayer();
      this.map.once("idle", this.calculateCenter);
    },
    calculateCenter() {
      if (this.map) {
        let features = this.map.querySourceFeatures("state-data", {
          sourceLayer: "points"
        });

        let minX = features[0].geometry.coordinates[0],
          maxX = features[0].geometry.coordinates[0],
          minY = features[0].geometry.coordinates[1],
          maxY = features[0].geometry.coordinates[1];
        for (let feature of features) {
          let temp = feature.geometry.coordinates;
          if (temp[0] < minX) minX = temp[0];
          if (temp[0] > maxX) maxX = temp[0];
          if (temp[1] < minY) minY = temp[1];
          if (temp[1] > maxY) maxY = temp[1];
        }

        this.map.fitBounds(
          [
            [minX, minY],
            [maxX, maxY]
          ],
          { padding: { top: 25, bottom: 25, left: 25, right: 25 } }
        );
      }
    },
    renderLayer(filter = ["all"]) {
      if (this.map.getLayer("points")) {
        this.map.removeLayer("points");
      }

      this.map.addLayer({ ...this.options, filter });
    },
    getFilters() {
      const conditions = Object.keys(this.filters)
        .filter(type => !!this.filters[type])
        .map(type => [
          "==",
          ["get", type, ["get", "project"]],
          this.filters[type]
        ]);
      if (this.search) {
        conditions.push([
          "in",
          this.search,
          ["get", "Title", ["get", "project"]]
        ]);
      }
      return ["all", ...conditions];
    },
    clicked(map, e) {
      if (e.features) {
        const coordinates = e.features[0].geometry.coordinates.slice();

        // Ensure that if the map is zoomed out such that multiple
        // copies of the feature are visible, the popup appears
        // over the copy being pointed to.
        while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
          coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
        }

        new Popup()
          .setLngLat({ lng: coordinates[0], lat: coordinates[1] })
          .setHTML('<div id="vue-popup-content"></div>')
          .addTo(map);

        new PopupContent({
          propsData: { feature: e.features[0] }
        }).$mount("#vue-popup-content");

        map.flyTo({ center: e.features[0].geometry.coordinates });
      }
    },
    mouseEntered(map) {
      map.getCanvas().style.cursor = "pointer";
    },
    mouseLeft(map) {
      map.getCanvas().style.cursor = "";
    }
  }
};
</script>
<style>
#map {
  width: 100%;
  height: 100vh;
  position: absolute;
  top: 0;
  bottom: 0;
}
</style>