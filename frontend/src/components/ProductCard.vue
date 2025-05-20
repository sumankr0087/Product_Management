<template>
    <div class="bg-white rounded-lg shadow overflow-hidden hover:shadow-lg transition-shadow duration-300">
        <div class="h-48 overflow-hidden relative">
            <img :src="product.image" :alt="product.title" class="w-full h-full object-contain p-4" />
        </div>
        <div class="p-4">
            <div class="flex justify-between items-start">
                <h2 class="text-lg font-semibold text-gray-900 line-clamp-1">{{ product.title }}</h2>
                <span class="bg-blue-100 text-blue-800 text-xs font-medium px-2.5 py-0.5 rounded">
                    {{ product.category }}
                </span>
            </div>
            <p class="mt-2 text-gray-600 text-sm line-clamp-2">{{ product.description }}</p>
            <div class="mt-4 flex justify-between items-center">
                <span class="text-xl font-bold text-gray-900">${{ product.price.toFixed(2) }}</span>
                <div class="flex space-x-2">
                    <button @click="$emit('edit', product)"
                        class="p-2 text-blue-600 hover:text-blue-800 rounded-full hover:bg-blue-100">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 24 24" fill="none"
                            stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"></path>
                            <path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"></path>
                        </svg>
                    </button>
                    <button @click="$emit('delete', product)"
                        class="p-2 text-red-600 hover:text-red-800 rounded-full hover:bg-red-100">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 24 24" fill="none"
                            stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <polyline points="3 6 5 6 21 6"></polyline>
                            <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
                            <line x1="10" y1="11" x2="10" y2="17"></line>
                            <line x1="14" y1="11" x2="14" y2="17"></line>
                        </svg>
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>
  
<script setup>
defineProps({
    product: {
        type: Object,
        required: true
    }
});

defineEmits(['edit', 'delete']);
</script>