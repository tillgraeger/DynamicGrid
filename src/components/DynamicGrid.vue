<script lang="ts">
import Vue, { ref } from "vue";
import axios from "axios";
import DynamicGridRow from "./DynamicGridRow.vue";
import LoadingSpinner from "./LoadingSpinner.vue";

export default Vue.extend({
  name: "DynamicGrid",
  components: {
    LoadingSpinner,
    DynamicGridRow,
  },
  setup() {
    const elements = ref(new Array<any>());
    const loading = ref(true);
    const pageCounter = ref(1);

    function loadMoreOverflow() {
      const listElm = document.querySelector("#infiniteGrid");
      if (
        listElm != null &&
        listElm.scrollTop + listElm.clientHeight >= listElm.scrollHeight
      ) {
        handleLoading();
      }
    }

    function getRows(arr: Array<any>, rows: number) {
      var result = [];
      for (let i = 0; i < arr.length; i += rows) {
        const chunk = arr.slice(i, i + rows);
        result.push({ rowID: i, rowElements: chunk });
      }
      return result;
    }

    function handleLoading() {
      loading.value = true;
      axios
        .get(
          `http://products-api.local.de/api/products?page=${pageCounter.value}`
        )
        .then((json) => {
          json.data["hydra:member"].forEach((element: any) => {
            element.big = false;
          });
          setTimeout(() => {
            getRows(json.data["hydra:member"], 3).forEach((row) => {
              row.rowElements[0].big = true;
              elements.value.push(row);
            });
            loading.value = false;
            pageCounter.value++;
          }, 250);
        });
    }
    axios
      .get(
        `http://products-api.local.de/api/products?page=${pageCounter.value}`
      )
      .then((res) => {
        res.data["hydra:member"].forEach((element: any) => {
          element.big = false;
        });
        const rows = getRows(res.data["hydra:member"], 3);
        rows.forEach((element) => {
          element.rowElements[0].big = true;
        });
        elements.value = rows;
        pageCounter.value++;
        loading.value = false;
      });
    return {
      elements,
      loading,
      loadMoreOverflow,
    };
  },
});
</script>

<template>
  <div id="infiniteGrid" @scroll="loadMoreOverflow" class="grid-container">
    <div class="md:grid grid-cols-2 grid-row-1 gap-x-2">
      <DynamicGridRow
        v-for="row in elements"
        :row="row"
        :key="elements.indexOf(row)"
        :positionOfBigElement="elements.indexOf(row) % 2 == 0"
      />
    </div>
    <Transition name="loadingFade">
      <LoadingSpinner v-if="loading" />
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
