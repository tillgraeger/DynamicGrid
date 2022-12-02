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
    //---data---

    const modalItem = ref({});
    const nextItem = ref(new Array<any>());

    const toPosition = 1;

    //---functions---
    // setup function to initalize rows to have different styling
    const initializeSetup = () => {
      var positionCounter = 0;
      for (let i = 0; i < props.initialElements.length; i++) {
        const row = props.initialElements[i];
        const position = positionCounter % 2 == 0 ? 0 : toPosition;
        row.rowElements.forEach((element) => {
          if (element.big) {
            var index = row.rowElements.indexOf(element);
            var e = row.rowElements[position];
            row.rowElements[position] = element;
            row.rowElements[index] = e;
          }
        });
        positionCounter++;
      }
    };

    // setup help array for nextItem
    const initializeHelpArray = () => {
      for (let i = 0; i < props.initialElements.length; i++) {
        const row = props.initialElements[i];
        var rowArray = { idCounter: 1, ids: new Array<any>() };
        row.rowElements.forEach((element) => {
          rowArray.ids.push(element.id);
        });
        nextItem.value.push({ rowArray, rowID: i });
      }
    };

    // emit if scrolled to bottom
    const loadMoreOverflow = () => {
      const listElm = document.querySelector("#infiniteGrid");

      if (
        listElm != null &&
        listElm.scrollTop + listElm.clientHeight >= listElm.scrollHeight
      ) {
        context.emit("loading");
      }
    };

    initializeHelpArray();
    initializeSetup();

    watch(
      () => props.loadingState,
      (loading) => {
        if (!loading) {
          initializeHelpArray();
          initializeSetup();
        }
      }
    );
    return {
      modalItem,
      nextItem,
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
      :nextItem="nextItem[initialElements.indexOf(row)]"
      :key="initialElements.indexOf(row)"
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

.modalFade-enter-active,
.modalFade-leave-active {
  transition: opacity 300ms;
}
.modalFade-enter,
.modalFade-leave-to {
  opacity: 0;
}
</style>
