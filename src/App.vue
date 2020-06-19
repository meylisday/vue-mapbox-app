<template>
  <div id="app">
    <div class="text-left pl-4 mt-3 position-fixed filter d-flex flex-column">
      <b-button v-b-toggle.sidebar-1>Filter</b-button>
      <b-button @click="emitZoom()" class="mt-3">Zoom to bounds</b-button>
    </div>
    <b-sidebar id="sidebar-1" title="Filter" shadow>
      <div class="px-3 py-2">
        <b-form-input v-model="search" placeholder="Enter title"></b-form-input>
        <Dropdown v-for="filter in Object.keys(availableFilters)" :label="filter" :key="filter" :options="availableFilters[filter]" @on-select="handleselect">{{ filter}}</Dropdown>
      </div>
    </b-sidebar>

    <Map :filters="geoDataFilters" :search="search"></Map>
  </div>
</template>

<script>
const Dropdown = () => import("./components/Dropdown.vue");
const Map = () => import("./components/Map.vue");
import { EventBus } from "./event-bus.js";

export default {
  name: "App",
  data() {
    return {
      geoDataFilters: {},
      features: [],
      availableFilters: {},
      search: "",
      filterBy: ["Suburb", "Stage", "Category", "SubCategory", "Status", "Council"]
    };
  },
  components: {
    Dropdown,
    Map
  },
  mounted() {
    fetch(
      "https://bitbucket.org/idda/coding-challenges/raw/88be221f75a1b108c9e5f7222906b2735c147ac8/testBlob.json"
    )
      .then(response => response.json())
      .then(data => {
        for (let filter of this.filterBy) {
          let unique = [
            ...new Set(
              data.features.map(item => item.properties.project[filter])
            )
          ];
          this.availableFilters = Object.assign({}, this.availableFilters,{
            [filter]: unique
          });
        }
      });
  },
  methods: {
    handleselect(selected) {
      return (this.geoDataFilters = Object.assign({}, this.geoDataFilters, {
        [selected.type]: selected.value
      }));
    },
    emitZoom() {
      EventBus.$emit("zoom");
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
body {
  width: 100%;
  height: 100%;
}
.filter {
  z-index: 10;
}
</style>
