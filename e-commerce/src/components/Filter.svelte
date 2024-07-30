<script>
    import { onMount } from "svelte";
    import { writable } from "svelte/store";
  
    // Create writable stores
    const filterItem = writable("All categories");
    const searchTerm = writable("");
    const sorting = writable("default");
    const minPrice = writable(null);
    const maxPrice = writable(null);
    const categories = writable([]);
    const products = writable([]);
    const filteredProducts = writable([]);
    const isOpen = writable(false);
    const loading = writable(false);
    const error = writable(null);
    const selectedSort = writable("default");
  
    // Toggle dropdown visibility
    function toggleDropdown() {
      isOpen.update(n => !n);
    }
  
    // Set filter and apply the filter
    function setFilter(category) {
      filterItem.set(category);
      isOpen.set(false);
      applyFilter();
    }
  
    // Handle search input change
    function handleSearch(event) {
      searchTerm.set(event.target.value);
      applyFilter();
    }
  
    // Handle price range change
    function handlePriceChange(event) {
      const { name, value } = event.target;
      if (name === "minPrice") {
        minPrice.set(value ? parseFloat(value) : null);
      } else if (name === "maxPrice") {
        maxPrice.set(value ? parseFloat(value) : null);
      }
      applyFilter();
    }
  
    // Handle sorting change
    function handleSorting(event) {
      sorting.set(event.target.value);
      applyFilter();
    }
  
    // Apply filter and sorting
    function applyFilter() {
      let currentFilter, currentSearchTerm, currentSorting, min, max;
  
      // Get current values from writable stores
      filterItem.subscribe(value => currentFilter = value)();
      searchTerm.subscribe(value => currentSearchTerm = value.toLowerCase())();
      sorting.subscribe(value => currentSorting = value)();
      minPrice.subscribe(value => min = value)();
      maxPrice.subscribe(value => max = value)();
  
      products.subscribe(allProducts => {
        // Apply category and price range filter
        const filteredByCategory = allProducts.filter(product => {
          const matchesCategory = currentFilter === "All categories" || product.category === currentFilter;
          const matchesSearch = product.title.toLowerCase().includes(currentSearchTerm);
          const matchesPrice = (min === null || product.price >= min) && (max === null || product.price <= max);
          return matchesCategory && matchesSearch && matchesPrice;
        });
  
        // Apply sorting
        applySorting(filteredByCategory, currentSorting);
      })();
    }
  
    // Apply sorting to the filtered list
    function applySorting(filteredList, sortingOrder) {
      const sortedList = [...filteredList];
      switch (sortingOrder) {
        case "low":
          sortedList.sort((a, b) => a.price - b.price);
          break;
        case "high":
          sortedList.sort((a, b) => b.price - a.price);
          break;
        default:
          sortedList.sort((a, b) => a.id - b.id);
      }
      filteredProducts.set(sortedList);
    }
  
    async function getProducts() {
      loading.set(true);
      error.set(null);
      try {
        let response = await fetch('https://fakestoreapi.com/products');
        if (!response.ok) {
          throw new Error("Failed to fetch products");
        }
        const data = await response.json();
        products.set(data);
        categories.set([...new Set(data.map(product => product.category))]);
        filteredProducts.set([...data]); // Set initial filtered products
      } catch (err) {
        error.set(err.message);
      } finally {
        loading.set(false);
        sortProducts();
      }
    }
  
    async function getProductsByCategory(category) {
      loading.set(true);
      error.set(null);
      try {
        let response = await fetch(`https://fakestoreapi.com/products/category/${category}`);
        if (!response.ok) {
          throw new Error("Failed to fetch products");
        }
        const data = await response.json();
        products.set(data);
        filteredProducts.set([...data]);
      } catch (err) {
        error.set(err.message);
      } finally {
        loading.set(false);
        sortProducts();
      }
    }
  
    function sortProducts() {
      selectedSort.subscribe(value => {
        sorting.set(value);
        applyFilter();
      })();
    }
  
    onMount(() => {
      getProducts();
    });
  </script>
  <!-- Your HTML template -->
  <div class="grid justify-center">
    <!-- Filter and Sort -->
    <div class="grid lg:flex gap-y-4 gap-x-48 lg:items-start mt-3 mx-auto justify-center">
      <!-- filter -->
      <form on:submit|preventDefault={applyFilter}>
        <div class="flex lg:w-[31.25rem] sm:w-[95%] md:w-full relative">
          <button
            on:click={toggleDropdown}
            id="dropdown-button"
            class="flex-shrink-0 z-10 inline-flex items-center py-2.5 px-4 text-sm font-medium text-center text-gray-900 bg-gray-100 border border-gray-300 rounded-s-lg hover:bg-gray-200"
            type="button"
          >
            <span>{$filterItem}</span>
            <svg
              class="w-2.5 h-2.5 ms-2.5"
              aria-hidden="true"
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 10 6"
            >
              <path
                stroke="currentColor"
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="m1 1 4 4 4-4"
              />
            </svg>
          </button>
          {#if $isOpen}
            <div
              on:click|self={() => isOpen.set(false)}
              id="dropdown"
              class="z-10 absolute top-[100%] bg-white divide-y divide-gray-100 rounded-lg shadow w-44"
            >
              <ul class="py-2 text-sm text-gray-700" aria-labelledby="dropdown-button">
                <li
                  on:click={() => setFilter('All categories')}
                  class="inline-flex w-full px-4 py-2 hover:bg-gray-100 cursor-pointer"
                >
                  All categories
                </li>
                {#each $categories as category}
                  <li>
                    <button
                      on:click={() => setFilter(category)}
                      type="button"
                      class="inline-flex w-full px-4 py-2 hover:bg-gray-100"
                    >
                      {category}
                    </button>
                  </li>
                {/each}
              </ul>
            </div>
          {/if}
          <div class="relative w-full">
            <input
              type="search"
              id="search-dropdown"
              name="searchInput"
              class="p-2.5 w-full z-20 text-sm text-gray-900 bg-gray-50 rounded-e-lg border-s-gray-50 border-s-2 border border-gray-300 focus:ring-blue-500 focus:border-blue-500"
              placeholder="Search products..."
              on:input={handleSearch}
            />
            <button
              type="submit"
              class="absolute top-0 end-0 p-2.5 text-sm font-medium h-full text-white bg-blue-700 rounded-e-lg border border-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300"
            >
              <svg
                class="w-4 h-4"
                aria-hidden="true"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 20 20"
              >
                <path
                  stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="m19 19-4-4m0-7A7 7 0 1 1 1 8a7 7 0 0 1 14 0Z"
                />
              </svg>
              <span class="sr-only">Search</span>
            </button>
          </div>
        </div>
      </form>
  
      <!-- Sort -->
      <div class="flex sm:w-[95%] max-w-[21rem] md:w-full">
        <label for="sort" class="w-20 my-auto font-semibold">
          Sort by:
        </label>
        <select
          bind:value={$sorting}
          on:change={() => applyFilter()} 
          id="sort"
          class="p-2.5 w-full text-sm text-gray-900 bg-gray-50 rounded-e-lg border-s-gray-50 border-s-2 border border-gray-300 focus:ring-blue-500 focus:border-blue-500"
        >
          <option value="default">Default</option>
          <option value="low">Price: Low to High</option>
          <option value="high">Price: High to Low</option>
        </select>
      </div>
  
      <!-- Price Range -->
      <div class="flex sm:w-[95%] max-w-[21rem] md:w-full gap-x-2">
        <input
          type="number"
          name="minPrice"
          placeholder="Min Price"
          class="p-2.5 text-sm text-gray-900 bg-gray-50 rounded-e-lg border-s-gray-50 border-s-2 border border-gray-300 focus:ring-blue-500 focus:border-blue-500"
          on:input={handlePriceChange}
        />
        <input
          type="number"
          name="maxPrice"
          placeholder="Max Price"
          class="p-2.5 text-sm text-gray-900 bg-gray-50 rounded-e-lg border-s-gray-50 border-s-2 border border-gray-300 focus:ring-blue-500 focus:border-blue-500"
          on:input={handlePriceChange}
        />
      </div>
    </div>
  </div>
  