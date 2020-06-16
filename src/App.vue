<template>
  <div id="app">
      <div class="text-left pl-4 mt-3 position-fixed filter">
        <b-button v-b-toggle.sidebar-1>Filter</b-button>
      </div>
      <b-sidebar id="sidebar-1" title="Filter" shadow>
        <div class="px-3 py-2">
          <b-form-input v-model="search" placeholder="Enter title"></b-form-input>
          <Dropdown :label="'Suburb'" :options="suburbs" @on-select="handleselect"></Dropdown>
          <Dropdown :label="'Stage'" :options="stages" @on-select="handleselect"></Dropdown>
          <Dropdown :label="'Category'" :options="categories" @on-select="handleselect"></Dropdown>
          <Dropdown :label="'SubCategory'" :options="subcategories" @on-select="handleselect"></Dropdown>
          <Dropdown :label="'Status'" :options="statuses" @on-select="handleselect"></Dropdown>
          <Dropdown :label="'Council'" :options="councils" @on-select="handleselect"></Dropdown>
        </div>
      </b-sidebar>
      <Map :filters="geoDataFilters" :search="search"></Map>
    </div>
</template>

<script>
import Dropdown from "./components/Dropdown.vue";
import Map from "./components/Map.vue";

export default {
  name: "App",
  data() {
    return {
      geoDataFilters: {},
      suburbs: [
        "SYDNEY",
        "LISMORE",
        "ASHFIELD",
        "PYRMONT",
        "SYDNEY SOUTH",
        "DARLING HARBOUR",
        "CIRCULAR QUAY",
        "HAYMARKET",
        "PARRAMATTA",
        "NORTH SYDNEY",
        "WINDSOR",
        "MOONEE BEACH",
        "CONCORD REPATRIATION HOSPITAL"
      ],
      stages: [
        "DA Approved",
        "DA Pending",
        "Construction",
        "DA Refused",
        "Other"
      ],
      categories: [
        "HOSPITALITY",
        "COMMERCIAL PREMISES",
        "RETAIL",
        "SOCIAL",
        "PUBLIC BUILDINGS & FACILITIES",
        "RESIDENTIAL",
        "SPORTS",
        "EDUCATION",
        "CIVIL ENGINEERING",
        "ENTERTAINMENT"
      ],
      subcategories: [
        "RESTAURANTS, CANTEENS",
        "CLUBS (LICENSED), LICENSED PREMISES, CASINOS, TAVERNS",
        "COMMERCIAL COMPLEXES - SHOPS, OFFICES",
        "LANDSCAPING, FENCING, ENTRY STATEMENTS, MONUMENTS, FOUNTAINS",
        "AMENITIES, DRESSING ROOMS",
        "SHOPS, SHOPPING CENTRES & ARCADES, SUPERMARKETS",
        "HOTELS, SERVICED APARTMENTS",
        "OFFICES",
        "CAR PARKS (MULTI-STOREY)",
        "FIRE STATIONS, SES",
        "TAKE-AWAY OUTLETS",
        "LIBRARY, ARCHIVES, GALLERIES, MUSEUMS, INTERPRETIVE CENTRES, TOURIST INFO",
        "SWIMMING BATHS & ASSOC. FACILITIES, SAUNAS, SPAS, SOLARIA",
        "INDOOR SPORTS & LEISURE CENTRES, BOWLING ALLEYS, SQUASH COURTS, GYMNASIUMS",
        "RELIGIOUS BUILDINGS, CIVIL CHAPELS",
        "TERTIARY - COLLEGES, INSTITUTES, UNIVERSITIES",
        "SCHOOLS - PRIMARY, SECONDARY (UP TO YR 12)",
        "KINDERGARTENS, DAY NURSERIES, CHILD CARE CENTRES",
        "TUNNELS, SUBWAYS",
        "LAW COURTS",
        "CONFERENCE & RECEPTION CENTRES & FACILITIES",
        "FED, STATE, LOCAL GOV'T OFFICES, EMBASSIES, JOB CENTRES,CIVIC CENTRES, TOWNHALLS",
        "UNITS, APARTMENTS, FLATS, TOWNHOUSES, VILLAS",
        "BANKS",
        "SURGERIES, MEDICAL CENTRES",
        "BACKPACKER ACCOMMODATION",
        "THEATRES, CINEMAS",
        "SPORTS GROUND HARD SURFACE (TENNIS COURTS, BASKETBALL & SKATEPARKS)",
        "RESIDENTIAL SUBDIVISION",
        "POWER STATIONS, SUBSTATIONS, WINDFARMS, PHONE EXCHANGES"
      ],
      statuses: [
        "Possible",
        "Firm",
        "Commenced",
        "Deferred",
        "No further info available",
        "Abandoned",
        "Registrations"
      ],
      councils: [
        "SYDNEY",
        "LISMORE",
        "ASHFIELD",
        "PARRAMATTA",
        "NORTH SYDNEY",
        "HAWKESBURY",
        "COFFS HARBOUR",
        "CANADA BAY"
      ],
      search: ''
    };
  },
  components: {
    Dropdown,
    Map
  },
  methods: {
    handleselect(selected) {
      return (this.geoDataFilters = Object.assign({}, this.geoDataFilters, {
        [selected.type]: selected.value
      }));
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
body {
  width: 100%;
  height: 100%;
}
.filter{
  z-index: 10;
}
</style>
