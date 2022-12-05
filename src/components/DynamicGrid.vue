<script lang="ts">
import Vue, { ref, watch } from "vue";
import DynamicGridRow from "./DynamicGridRow.vue";
import LoadingSpinner from "./LoadingSpinner.vue";

export default Vue.extend({
  name: "DynamicGrid",
  props: ["initialElements", "loadingState"],
  components: {
    LoadingSpinner,
    DynamicGridRow,
  },
  setup(props, context) {
    // emit if scrolled to bottom
    function loadMoreOverflow() {
      const listElm = document.querySelector("#infiniteGrid");

      if (
        listElm != null &&
        listElm.scrollTop + listElm.clientHeight >= listElm.scrollHeight
      ) {
        context.emit("loading");
      }
    }
    return {
      loadMoreOverflow,
    };
  },
});
</script>

<template>
  <div id="infiniteGrid" @scroll="loadMoreOverflow" class="grid-container">
    <DynamicGridRow
      v-for="row in initialElements"
      :row="row"
      :key="initialElements.indexOf(row)"
      :positionOfBigElement="initialElements.indexOf(row) % 2 == 0"
    />
    <Transition name="loadingFade">
      <LoadingSpinner v-if="loadingState" />
    </Transition>
  </div>
</template>

<style>
.loadingFade-enter-active,
.loadingFade-leave-active {
  transition: opacity 0.5s;
}
.loadingFade-enter,
.loadingFade-leave-to {
  opacity: 0;
}
</style>
