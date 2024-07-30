// src/stores/productStore.js
import { writable, get } from 'svelte/store';

export const products = writable([]);
export const originalProducts = writable([]);
export const loading = writable(false);
export const error = writable(false);
export const sorting = writable('default');
export const searchTerm = writable('');
export const filterItem = writable('All categories');

export const fetchProducts = async () => {
  if (get(filterItem) !== 'All categories') {
    try {
      loading.set(true);
      const response = await fetch(`https://fakestoreapi.com/products/category/${get(filterItem)}`);
      if (!response.ok) {
        throw new Error('Data fetching failed, please check your network connection');
      }
      const data = await response.json();
      products.set(data);
      originalProducts.set(JSON.parse(JSON.stringify(data)));
      loading.set(false);
    } catch (err) {
      error.set(err.message);
      loading.set(false);
    } finally {
      sortProducts();
      searchProducts();
    }
  } else {
    try {
      loading.set(true);
      const response = await fetch('https://fakestoreapi.com/products');
      if (!response.ok) {
        throw new Error('Data fetching failed, please check your network connection and reload');
      }
      const data = await response.json();
      products.set(data);
      originalProducts.set(JSON.parse(JSON.stringify(data)));
      loading.set(false);
    } catch (err) {
      error.set(err.message);
      loading.set(false);
    } finally {
      sortProducts();
      searchProducts();
    }
  }
};

export const sortProducts = () => {
  if (get(sorting) !== 'default') {
    products.update(items =>
      items.sort((a, b) => (get(sorting) === 'low' ? a.price - b.price : b.price - a.price))
    );
  } else {
    originalProducts.subscribe(original => {
      products.set(JSON.parse(JSON.stringify(original)));
    });
  }
};

export const searchProducts = () => {
  originalProducts.subscribe(original => {
    if (get(searchTerm).trim() !== '') {
      const filteredProducts = original.filter(product =>
        product.title.toLowerCase().includes(get(searchTerm).toLowerCase())
      );
      products.set(JSON.parse(JSON.stringify(filteredProducts)));
    } else {
      products.set(JSON.parse(JSON.stringify(original)));
    }
  });
};
