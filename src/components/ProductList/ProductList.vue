<template>
  <div>
    <div class="m-3">
      <div class="input-group mb-3">
      <input
        type="text"
        class="form-control"
        placeholder="Search"
        v-model="searchTerm"
        @input="handleSearch"
        :disabled="isLoading"
      />
      <button
        class="btn btn-outline-secondary"
        type="button"
        v-if="searchTerm"
        @click="clearSearch"
      >
        clear
      </button>
    </div>
    </div>

    <table class="table">
      <thead>
        <tr>
          <th>ID</th>
          <th>Name</th>
          <th>Description</th>
          <th>Category</th>
          <th>Brand</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>
        <slot v-if="!isLoading">
          <ProductItem
          v-for="product in products"
          :key="product.id"
          :product="product"
          />
        </slot>
        
        <LoadingSpinner :loading="isLoading" />
      </tbody>
    </table>

    <nav>
      <ul class="pagination">
        <li class="page-item" :class="{ disabled: currentPage === 0 }">
          <a class="page-link" href="#" @click="prevPage">Previous</a>
        </li>
        <li
          class="page-item"
          v-for="page in totalPages"
          :key="page"
          :class="{ active: currentPage === page - 1 }"
        >
          <a class="page-link" href="#" @click="changePage(page - 1)">{{
            page
          }}</a>
        </li>
        <li
          class="page-item"
          :class="{ disabled: currentPage === totalPages - 1 }"
        >
          <a class="page-link" href="#" @click="nextPage">Next</a>
        </li>
      </ul>
    </nav>
  </div>
</template>
  
  <script>
import { ref, reactive, watchEffect, onMounted, onUnmounted } from "vue";
import axios from "axios";
import ProductItem from "../ProductItem/ProductItem.vue";
import LoadingSpinner from "../Loader/Loader.vue";
import { debounce } from 'lodash';

export default {
  components: {
    ProductItem,
    LoadingSpinner,
  },
  setup() {
    const limit = 10;

    const searchTerm = ref('');
    const products = ref([]);
    const currentPage = ref(0);
    const isLoading = ref(false);
    // const skip = ref(0);
    const totalPages = ref(0);
    const debouncedSearch = debounce(fetchProductsBySearch, 500);

    const fetchProducts = async () => {
      const url = `https://dummyjson.com/products?limit=${limit}&skip=${
        currentPage.value * 10
      }`;

      isLoading.value = true;

      try {
        const response = await axios.get(url);
        products.value = response.data.products;
        totalPages.value = response.data.total / limit;
        isLoading.value = false;
      } catch (error) {
        console.error("Error fetching products:", error);
        isLoading.value = false;
      }
    };

    watchEffect(() => {
      fetchProducts();
    });

    const changePage = (page) => {
      currentPage.value = page;
    };

    const prevPage = () => {
      if (currentPage.value > 0) {
        currentPage.value--;
      }
    };

    const nextPage = () => {
      if (currentPage.value < totalPages.value - 1) {
        currentPage.value++;
      }
    };

    function handleSearch(event) {
      currentPage.value = 0; // Reset the current page when a new search term is entered
      totalPages.value = 0;
      debouncedSearch();
    };

    async function fetchProductsBySearch () {
      const url = `https://dummyjson.com/products/search?q=${searchTerm.value}&limit=${limit}&skip=${currentPage.value * limit}`;

      isLoading.value = true;

      try {
        const response = await axios.get(url);
        products.value = response.data.products;
        totalPages.value = Math.ceil(response.data.total / limit);
        isLoading.value = false;
      } catch (error) {
        console.error("Error fetching products:", error);
        isLoading.value = false;
      }
    };

    const clearSearch = () => {
      searchTerm.value  = ''
      fetchProducts()
    }

    return {
      products,
      currentPage,
      totalPages,
      isLoading,
      searchTerm,
      handleSearch,
      changePage,
      prevPage,
      nextPage,
      clearSearch
    };
  },
};
</script>
  
  <style>
.pagination {
  justify-content: center;
}
</style>
  