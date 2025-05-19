<template>
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
      <div
        v-for="product in products"
        :key="product.id"
        class="bg-white rounded shadow hover:shadow-md transition p-4 flex flex-col"
      >
        <!-- Product Image -->
        <img
          :src="product.image"
          :alt="product.title"
          class="w-full h-48 object-contain mb-4 rounded"
        />
  
        <!-- Product Info -->
        <h2 class="text-lg font-semibold mb-2 truncate">{{ product.title }}</h2>
        <p class="text-gray-700 mb-1"><span class="font-medium">Price:</span> ${{ product.price }}</p>
        <p class="text-gray-500 mb-4"><span class="font-medium">Category:</span> {{ product.category }}</p>
  
        <!-- Action Buttons -->
        <div class="mt-auto flex justify-between">
          <button
            @click="$emit('edit', product)"
            class="bg-yellow-400 hover:bg-yellow-500  px-4 py-1 rounded text-sm"
          >
            Edit
          </button>
          <button
            @click="$emit('delete', product.id)"
            class="bg-red-500 hover:bg-red-600 px-4 py-1 rounded text-sm"
          >
            Delete
          </button>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  defineProps(['products']);
  defineEmits(['edit', 'delete']);
  </script>
  