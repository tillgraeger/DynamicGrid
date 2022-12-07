<script lang="ts">
import Vue, { ref } from "vue";
import DynamicGridVue from "./components/DynamicGrid.vue";

export default Vue.extend({
  name: "App",
  components: {
    DynamicGridVue,
  },
});
</script>

<template>
  <div id="app">
    <div class="flex justify-center mt-4">
      <DynamicGridVue />
    </div>
  </div>
</template>
