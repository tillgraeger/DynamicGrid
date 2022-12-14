<script lang="ts">
import Vue, { ref } from "vue";
import DynamicGridModal from "./DynamicGridModal.vue";

export default Vue.extend({
  name: "DynamicGridElement",
  props: ["el", "moveLeft", "moveRight", "scaleLeft", "scaleRight"],
  components: { DynamicGridModal },
  setup(props, context) {
    //---data---
    const imageSource =
      "http://products-api.local.de/images/" + props.el.image_path;
    const loaded = ref(false);
    const isModalVisible = ref(false);

    //---functions---
    const toggle = () => {
      context.emit("toggle", props.el);
    };
    const imageLoaded = () => {
      loaded.value = true;
    };
    const onClick = () => {
      context.emit("click");
    };

    const openModal = () => {
      isModalVisible.value = true;
    };

    const closeModal = () => {
      isModalVisible.value = false;
    };

    return {
      toggle,
      imageLoaded,
      onClick,
      isModalVisible,
      openModal,
      closeModal,
      loaded,
      imageSource,
    };
  },
});
</script>

<template>
  <div
    class="grid-element-container"
    :class="{
      'grid-element-big': el.big,
      'grid-element-small': !el.big,
      'scaleUpRight mr-0 ml-auto': this.scaleLeft && el.big,
      scaleUpLeft: this.scaleRight && el.big,
      moveLeft: this.moveLeft && !el.big,
      moveRight: this.moveRight && !el.big,
    }"
    @click="openModal"
  >
    <img :src="imageSource" @load="imageLoaded" />
    <button
      type="button"
      v-if="el.big && loaded"
      @click.stop="toggle()"
      class="close-button"
    >
      <svg
        class="h-2 w-2"
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
        aria-hidden="true"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M6 18L18 6M6 6l12 12"
        />
      </svg>
    </button>

    <p>{{ el.name }}</p>

    <svg
      v-if="!loaded"
      role="status"
      class="loading-spinner"
      viewBox="0 0 100 101"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
    >
      <path
        d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
        fill="#E5E7EB"
      />
      <path
        d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
        fill="currentColor"
      />
    </svg>
    <Transition name="modalFade">
      <DynamicGridModal
        :modalElement="el"
        v-if="isModalVisible"
        @close="closeModal"
      />
    </Transition>
  </div>
</template>

<style>
.scaleUpLeft {
  animation: scaleUpLeft 600ms forwards;
  animation-timing-function: ease-in-out;
}

@keyframes scaleUpLeft {
  0% {
    height: 10rem;
    width: 5px;
  }
  100% {
    height: 20.5rem;
    width: 100%;
  }
}

.scaleUpRight {
  animation: scaleUpRight 600ms forwards;
  animation-timing-function: ease-in-out;
}

@keyframes scaleUpRight {
  0% {
    height: 10rem;
    width: 5px;
  }
  100% {
    height: 20.5rem;
    width: 100%;
  }
}

.moveLeft {
  animation: moveLeft 600ms forwards;
  animation-timing-function: ease-in-out;
}

@keyframes moveLeft {
  0% {
    transform: translateX(10rem);
  }
  100% {
    transform: translateX(0px);
  }
}

.moveRight {
  animation: moveRight 600ms forwards;
  animation-timing-function: ease-in-out;
}

@keyframes moveRight {
  0% {
    transform: translateX(-10rem);
  }
  100% {
    transform: translateX(0px);
  }
}
</style>
