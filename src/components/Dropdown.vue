<template>
  <div>
    <div class="w-100 text-left">{{ label }}</div>
    <b-form-select v-model="selected" :options="formatOptions" @change="onChange"></b-form-select>
  </div>
</template>

<script>
export default {
  name: "Dropdown",
  props: ["label", "options"],
  data() {
    return {
      selected: ""
    };
  },
  computed: {
    formatOptions: function() {
      return [
        { value: "", text: "ALL" },
        ...this.options.map(item => ({ value: item, text: item }))
      ];
    }
  },
  methods: {
    onChange(value) {
      // if (this.onSelect && typeof this.onSelect === "function"){
      //   this.onSelect({type: this.label, value});
      // }

      this.$emit('on-select', {type: this.label, value})
    }
  }
};
</script>