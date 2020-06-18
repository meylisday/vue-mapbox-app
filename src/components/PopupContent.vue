<template>
  <div>
    <h6>{{ project.Title }}</h6>
    <span class="address">{{ project.Address }}</span>
    <span class="description">{{ project.Description }}</span>
  </div>
</template>
 
<script>
import Vue from 'vue'
export default Vue.extend({
  props: {
    feature: {
      required: true,
      type: Object,
    },
  },
  computed:{
    project: function(){
      return JSON.parse(this.feature.properties.project);
    }
  },
  methods: {
  },
})
</script>
<style>
.description{
  display: block;
  max-height: 200px;
  overflow-x: auto;
  margin-top:1rem;
}
.address{
  font-weight: bold;
}
</style>