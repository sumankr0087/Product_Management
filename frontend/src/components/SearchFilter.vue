<template>
    <div class="mb-6 flex flex-col md:flex-row gap-4 justify-between items-start md:items-center">
        <div class="w-full md:w-1/2">
            <div class="relative">
                <input v-model="searchQuery" type="text" placeholder="Search products..."
                    class="w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" />
                <div @click="clearSearch" v-if="searchQuery"
                    class="absolute right-3 top-4 text-gray-400 hover:text-gray-600">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 24 24" fill="none"
                        stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                        <line x1="18" y1="6" x2="6" y2="18"></line>
                        <line x1="6" y1="6" x2="18" y2="18"></line>
                    </svg>
                </div>
            </div>
        </div>
        <div class="flex gap-4 w-full md:w-auto">
            <select v-model="selectedCategory"
                class="px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                <option value="">All Categories</option>
                <option v-for="category in categories" :key="category" :value="category">
                    {{ category }}
                </option>
            </select>
            <div @click="$emit('add')"
                class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
                Add Product
            </div>
        </div>
    </div>
</template>
  
<script setup>
import { ref, watch } from 'vue';

const props = defineProps({
    categories: {
        type: Array,
        required: true
    }
});

const emit = defineEmits(['update:search', 'update:category', 'add']);

const searchQuery = ref('');
const selectedCategory = ref('');

const clearSearch = () => {
    searchQuery.value = '';
};

// Emit updates when search or category changes
watch(searchQuery, (newVal) => {
    emit('update:search', newVal);
});

watch(selectedCategory, (newVal) => {
    emit('update:category', newVal);
});
</script>