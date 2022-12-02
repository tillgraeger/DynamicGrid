<script lang="ts">
import Vue, { ref } from "vue";
import DynamicGridElement from "./DynamicGridElement.vue";

export default Vue.extend({
  name: "DynamicGridRow",
  props: ["row", "nextItem"],
  components: { DynamicGridElement },
  setup(props, context) {
    //---data---
    const idCounter = ref(props.nextItem);
    const moveLeft = ref(false);
    const moveRight = ref(false);
    const scaleLeft = ref(false);
    const scaleRight = ref(false);
    const rowElements = ref([]);

    const toPosition = 1;

    //---functions---
    // toggle animation variables
    const animate = (direction: boolean) => {
      if (direction) {
        moveLeft.value = true;
        scaleLeft.value = true;
        setTimeout(() => {
          moveLeft.value = false;
          scaleLeft.value = false;
        }, 1000);
      } else {
        moveRight.value = true;
        scaleRight.value = true;
        setTimeout(() => {
          moveRight.value = false;
          scaleRight.value = false;
        }, 1000);
      }
    };
    // switch two elements in an array
    const switchToPosition = (
      arr: Array<any>,
      fromIndex: number,
      toIndex: number
    ) => {
      const element = arr.splice(fromIndex, 1)[0];
      arr.splice(toIndex, 0, element);
    };

    // toggle size of element and put it on the right position in the array
    const toggleSize = (el: any) => {
      context.emit("nofavorite");
      var id = idCounter.value.rowArray.ids[idCounter.value.rowArray.idCounter];
      var fromIndex = rowElements.value
        .map((e) => {
          return e.id;
        })
        .indexOf(id);
      const toIndex = rowElements.value[0].big ? toPosition : 0;

      switchToPosition(rowElements.value, fromIndex, toIndex);
      rowElements.value[toIndex].big = !rowElements.value[toIndex].big;
      el.big = !el.big;

      if (
        idCounter.value.rowArray.idCounter <
        idCounter.value.rowArray.ids.length - 1
      ) {
        idCounter.value.rowArray.idCounter++;
      } else {
        idCounter.value.rowArray.idCounter = 0;
      }
      animate(toIndex == toPosition);
    };

    const openModal = (el: any) => {
      context.emit("open", el);
    };

    rowElements.value = props.row.rowElements;

    animate(rowElements.value[0].big);

    return {
      moveLeft,
      moveRight,
      scaleLeft,
      scaleRight,
      toggleSize,
      openModal,
    };
  },
});
</script>

<template>
  <div class="grid-row-container md:grid-row-container-desktop">
    <DynamicGridElement
      v-for="el in row.rowElements"
      :key="el.id"
      :el="el"
      :moveLeft="moveLeft"
      :moveRight="moveRight"
      :scaleLeft="scaleLeft"
      :scaleRight="scaleRight"
      @click="openModal(el)"
      @toggle="toggleSize(el)"
    />
  </div>
</template>
