<script lang="ts">
import Vue, { ref } from "vue";
import axios from "axios";
import DynamicGridVue from "./components/DynamicGrid.vue";

export default Vue.extend({
  name: "App",
  components: {
    DynamicGridVue,
  },
  setup() {
    const elements = ref(new Array<any>());
    const elementsDesktop = ref(new Array<any>());
    const loading = ref(true);
    const pageCounter = ref(1);

    const getRows = (arr: Array<any>, rows: number) => {
      var result = [];
      for (let i = 0; i < arr.length; i += rows) {
        const chunk = arr.slice(i, i + rows);
        result.push({ rowID: i, rowElements: chunk });
      }
      return result;
    };

    const handleLoading = () => {
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
    };
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

    axios
      .get(
        `http://products-api.local.de/api/products?page=${pageCounter.value}`
      )
      .then((res) => {
        res.data["hydra:member"].forEach((element: any) => {
          element.big = false;
        });
        const rows = getRows(res.data["hydra:member"], 9);
        rows.forEach((element) => {
          if (element.rowElements.length < 9) return;
          element.rowElements[0].big = true;
        });
        elementsDesktop.value = rows;
        pageCounter.value++;
        loading.value = false;
      });

    return { elements, elementsDesktop, loading, handleLoading };
  },
});
</script>

<template>
  <div id="app">
    <div class="flex justify-center mt-4" v-if="elements.length > 0">
      <DynamicGridVue
        @loading="handleLoading"
        :loadingState="loading"
        :initialElements="elements"
      />
    </div>
  </div>
</template>
