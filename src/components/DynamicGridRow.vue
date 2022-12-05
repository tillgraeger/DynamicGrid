<script lang="ts">
import Vue, { ref } from "vue";
import DynamicGridElement from "./DynamicGridElement.vue";

export default Vue.extend({
  name: "DynamicGridRow",
  props: ["row", "positionOfBigElement"],
  components: { DynamicGridElement },
  setup(props, context) {
    //---data---
    const rowElements = ref(props.row.rowElements);
    const bigPosition = ref(props.positionOfBigElement);

    //---functions---
    function pushFirstElementToEnd() {
      rowElements.value.push(rowElements.value.shift());
      bigPosition.value = !bigPosition.value;
    }

    return {
      rowElements,
      bigPosition,
      pushFirstElementToEnd,
    };
  },
});
</script>

<template>
  <div
    :class="{ moveLeft: bigPosition, moveRight: !bigPosition }"
    class="grid-row-container md:grid-row-container-desktop h-80"
  >
    <DynamicGridElement
      :class="{
        'grid-element-big': el == rowElements[0],
        'col-start-2': el == rowElements[0] && bigPosition,
      }"
      v-for="el in rowElements"
      :key="el.id"
      :el="el"
      :showButton="el == rowElements[0]"
      @click="pushFirstElementToEnd"
    />
  </div>
</template>

<style>
.moveLeft {
  animation: moveLeft 600ms;
  animation-timing-function: ease-in-out;
}

@keyframes moveLeft {
  0% {
    transform: translateX(20rem);
  }
  100% {
    transform: translateX(0px);
  }
}

.moveRight {
  animation: moveRight 600ms;
  animation-timing-function: ease-in-out;
}

@keyframes moveRight {
  0% {
    transform: translateX(-20rem);
  }
  100% {
    transform: translateX(0px);
  }
}
</style>
