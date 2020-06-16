<template>
  <div id="map">
    <Mapbox
      :access-token="accessToken"
      :map-options="{
        style: mapStyle,
        center: [151.209152, -33.875305],
        zoom: 13,
      }"
      @map-load="loaded"
    />
  </div>
</template>

<script>

const Mapbox  = () => import("mapbox-gl-vue");

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
        type: "symbol",
        source: "state-data",
        layout: {
          "icon-image": "circle-11",
          "text-field": ["get", "Title", ["get", "project"]],
          "text-font": ["Open Sans Semibold", "Arial Unicode MS Bold"],
          "text-offset": [0, 0.6],
          "text-anchor": "top"
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
  methods: {
    loaded(map) {
      this.map = map;
      this.map.addSource("state-data", this.source);
      this.renderLayer();
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
    }
  }
};
</script>
<style>
#map {
  width: 100%;
  height: 100vh;
}
</style>