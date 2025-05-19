<template>
    <div class="min-h-screen bg-gray-50">
      <header class="bg-white shadow">
        <div class="max-w-7xl mx-auto px-4 py-6 sm:px-6 lg:px-8">
          <h1 class="text-3xl font-bold text-gray-900">Product Management Dashboard</h1>
        </div>
      </header>
      <main class="max-w-7xl mx-auto px-4 py-6 sm:px-6 lg:px-8">
        <SearchFilter
          :categories="categories"
          @update:search="searchQuery = $event"
          @update:category="selectedCategory = $event"
          @add="openAddForm"
        />
  
        <LoadingSpinner v-if="loading" />
  
        <div
          v-else-if="error"
          class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative my-4"
        >
          <strong class="font-bold">Error!</strong>
          <span class="block sm:inline"> {{ error }}</span>
        </div>
  
        <div
          v-else-if="filteredProducts.length > 0"
          class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6"
        >
          <ProductCard
            v-for="product in filteredProducts"
            :key="product.id"
            :product="product"
            @edit="editProduct"
            @delete="confirmDelete"
          />
        </div>
  
        <div v-else class="text-center py-12">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-12 w-12 mx-auto text-gray-400"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M20 13V6a2 2 0 00-2-2H6a2 2 0 00-2 2v7m16 0v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5m16 0h-2.586a1 1 0 00-.707.293l-2.414 2.414a1 1 0 01-.707.293h-3.172a1 1 0 01-.707-.293l-2.414-2.414A1 1 0 006.586 13H4"
            />
          </svg>
          <h3 class="mt-2 text-lg font-medium text-gray-900">No products found</h3>
          <p class="mt-1 text-gray-500">Try adjusting your search or filter to find what you're looking for.</p>
        </div>
  
        <ProductForm
          v-if="showForm"
          :product="formData"
          :categories="categories"
          :is-submitting="formSubmitting"
          @close="closeForm"
          @submit="submitForm"
        />
  
        <DeleteConfirmation
          v-if="showDeleteConfirm"
          :product="productToDelete"
          :is-submitting="deleteSubmitting"
          @cancel="showDeleteConfirm = false"
          @confirm="handleDeleteProduct"
        />
      </main>
    </div>
  </template>
  
  <script setup>
  import { ref, computed, onMounted, reactive } from 'vue';
  import {
    fetchProducts,
    addProduct,
    updateProduct,
    deleteProduct as apiDeleteProduct
  } from '../api/products';
  
  import ProductCard from '../components/ProductCard.vue';
  import ProductForm from '../components/ProductForm.vue';
  import DeleteConfirmation from '../components/DeleteConfirmation.vue';
  import SearchFilter from '../components/SearchFilter.vue';
  import LoadingSpinner from '../components/LoadingSpinner.vue';
  
  // State
  const products = ref([]);
  const categories = ref([]);
  const loading = ref(true);
  const error = ref(null);
  const searchQuery = ref('');
  const selectedCategory = ref('');
  const showForm = ref(false);
  const showDeleteConfirm = ref(false);
  const formSubmitting = ref(false);
  const deleteSubmitting = ref(false);
  const productToDelete = ref(null);
  
  // Form data
  const formData = reactive({
    id: null,
    title: '',
    price: 0,
    description: '',
    category: '',
    image: ''
  });
  
  // Computed
  const filteredProducts = computed(() => {
    let result = products.value;
  
    if (selectedCategory.value) {
      result = result.filter((product) => product.category === selectedCategory.value);
    }
  
    if (searchQuery.value) {
      const query = searchQuery.value.toLowerCase();
      result = result.filter((product) =>
        product.title.toLowerCase().includes(query) ||
        product.description.toLowerCase().includes(query)
      );
    }
  
    return result;
  });
  
  // Methods
  const loadProducts = async () => {
    loading.value = true;
    error.value = null;
  
    try {
      const data = await fetchProducts();
      products.value = data;
  
      const uniqueCategories = [...new Set(data.map((product) => product.category))];
      categories.value = uniqueCategories;
    } catch (err) {
      console.error('Error loading products:', err);
      error.value = err.message || 'Failed to load products.';
    } finally {
      loading.value = false;
    }
  };
  
  const openAddForm = () => {
    Object.assign(formData, {
      id: null,
      title: '',
      price: 0,
      description: '',
      category: '',
      image: ''
    });
    showForm.value = true;
  };
  
  const editProduct = (product) => {
    Object.assign(formData, product);
    showForm.value = true;
  };
  
  const confirmDelete = (product) => {
    productToDelete.value = product;
    showDeleteConfirm.value = true;
  };
  
  const submitForm = async (formData) => {
    formSubmitting.value = true;
  
    try {
      if (formData.id) {
        await updateProduct(formData.id, formData);
        const index = products.value.findIndex((p) => p.id === formData.id);
        if (index !== -1) {
          products.value[index] = { ...formData };
        }
      } else {
        const newProduct = await addProduct(formData);
        products.value.unshift({
          ...formData,
          id: newProduct.id || Date.now()
        });
      }
      showForm.value = false;
    } catch (err) {
      console.error('Error submitting form:', err);
      error.value = err.message || 'Form submission failed.';
    } finally {
      formSubmitting.value = false;
    }
  };
  
  const handleDeleteProduct = async () => {
    if (!productToDelete.value) return;
  
    deleteSubmitting.value = true;
  
    try {
      await apiDeleteProduct(productToDelete.value.id);
      products.value = products.value.filter((p) => p.id !== productToDelete.value.id);
      showDeleteConfirm.value = false;
      productToDelete.value = null;
    } catch (err) {
      console.error('Error deleting product:', err);
      error.value = err.message || 'Failed to delete product.';
    } finally {
      deleteSubmitting.value = false;
    }
  };
  
  const closeForm = () => {
    showForm.value = false;
  };
  
  // Lifecycle
  onMounted(() => {
    loadProducts();
  });
  </script>
  