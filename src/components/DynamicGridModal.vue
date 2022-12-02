<script lang="ts">
import Vue, { ref } from "vue";

export default Vue.extend({
  name: "DynamicGridModal",
  props: ["modalElement"],
  setup(props, context) {
    const loaded = ref(false);
    const imageSource =
      "http://products-api.local.de/images/" + props.modalElement.image_path;

    const close = () => {
      context.emit("close");
    };
    const imageLoaded = () => {
      loaded.value = true;
    };
    const formatCurrency = (num: any) => {
      return new Intl.NumberFormat("de-DE", {
        style: "currency",
        currency: "EUR",
      }).format(num);
    };
    const getDiscount = () => {
      return (
        props.modalElement.price *
        (props.modalElement.discountPercentage * 0.01)
      );
    };
    const getPrice = (cents = true) => {
      if (cents) {
        return props.modalElement.price - getDiscount();
      } else {
        return Math.round(props.modalElement.price - getDiscount());
      }
    };
    const getCents = () => {
      const res = Math.round(
        (getPrice() - Math.floor(props.modalElement.price - getDiscount())) *
          100
      );
      if (res < 10) {
        return "0" + res.toString();
      } else {
        return res.toString();
      }
    };
    return {
      loaded,
      imageSource,
      close,
      imageLoaded,
      formatCurrency,
      getDiscount,
      getPrice,
      getCents,
    };
  },
});
</script>

<template>
  <div>
    <div @click.self="close" class="modal-background">
      <div class="modal-container">
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

        <div class="modal-image-container">
          <img :src="imageSource" @load="imageLoaded" />
          <button type="button" @click.stop="close" class="close-button">
            <svg
              class="h-6 w-6"
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
        </div>
        <div class="mx-2">
          <h1 class="font-mono px-2 font-semibold">
            {{ modalElement.name }}
          </h1>
          <div class="divider"></div>
          <div class="flex flex-col gap-2 px-2">
            <p class="text-sm">
              {{ this.formatCurrency(modalElement.price) }} UVP
            </p>
            <div class="flex flex-row items-center">
              <p class="text-3xl font-bold">{{ this.getPrice(false) }}</p>
              <div class="ml-1 flex flex-col">
                <p class="text-lg font-bold">{{ this.getCents() }}</p>
                <p class="-mt-4 font-black">,-</p>
              </div>
            </div>
            <p class="text-orange-600 text-sm font-medium">
              Du sparst {{ this.formatCurrency(this.getDiscount()) }}
            </p>
            <p class="text-blue-800 font-semibold">34 P</p>
          </div>
          <div class="divider"></div>
          <div class="flex flex-row justify-between">
            <p class="ml-2">Kategorie:</p>
            <p class="mr-2">{{ modalElement.category }}</p>
          </div>
          <div class="flex flex-row justify-between">
            <p class="ml-2">Marke:</p>
            <p class="mr-2">{{ modalElement.brand }}</p>
          </div>
          <div class="divider"></div>
          <div class="mb-2">
            <p class="ml-2">Beschreibung:</p>
            <p class="mx-2 text-justify">{{ modalElement.description }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
