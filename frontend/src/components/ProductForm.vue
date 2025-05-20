<template>
    <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50">
        <div class="bg-white rounded-lg shadow-xl max-w-md w-full max-h-[90vh] overflow-y-auto">
            <div class="p-6">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-xl font-semibold text-gray-900">
                        {{ isEditing ? 'Edit Product' : 'Add New Product' }}
                    </h2>
                    <button @click="$emit('close')" class="text-gray-400 hover:text-gray-500">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24"
                            stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M6 18L18 6M6 6l12 12" />
                        </svg>
                    </button>
                </div>
                <form @submit.prevent="$emit('submit', formData)">
                    <div class="space-y-4">
                        <div>
                            <label for="title" class="block text-sm font-medium text-gray-700">Title</label>
                            <input id="title" v-model="formData.title" type="text" required
                                class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500" />
                        </div>
                        <div>
                            <label for="price" class="block text-sm font-medium text-gray-700">Price ($)</label>
                            <input id="price" v-model.number="formData.price" type="number" step="0.01" min="0" required
                                class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500" />
                        </div>
                        <div>
                            <label for="category" class="block text-sm font-medium text-gray-700">Category</label>
                            <select id="category" v-model="formData.category" required
                                class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500">
                                <option v-for="category in categories" :key="category" :value="category">
                                    {{ category }}
                                </option>
                            </select>
                        </div>
                        <div>
                            <label for="description" class="block text-sm font-medium text-gray-700">Description</label>
                            <textarea id="description" v-model="formData.description" rows="3" required
                                class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"></textarea>
                        </div>
                        <div>
                            <label for="image" class="block text-sm font-medium text-gray-700">Image URL</label>
                            <input id="image" v-model="formData.image" type="url" required
                                class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500" />
                        </div>
                    </div>
                    <div class="mt-6 flex justify-end space-x-3">
                        <button type="button" @click="$emit('close')"
                            class="px-4 py-2 border border-gray-300 rounded-md shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500">
                            Cancel
                        </button>
                        <button type="submit" :disabled="isSubmitting"
                            class="px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium  bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 disabled:opacity-50 disabled:cursor-not-allowed">
                            {{ isSubmitting ? 'Saving...' : (isEditing ? 'Update' : 'Add') }}
                        </button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>
  
<script setup>
import { reactive, watch, computed } from 'vue';

const props = defineProps({
    product: {
        type: Object,
        default: null
    },
    categories: {
        type: Array,
        required: true
    },
    isSubmitting: {
        type: Boolean,
        default: false
    }
});

const emit = defineEmits(['close', 'submit']);

const isEditing = computed(() => !!props.product?.id);

const defaultFormData = {
    id: null,
    title: '',
    price: 0,
    description: '',
    category: '',
    image: ''
};

const formData = reactive({ ...defaultFormData });

// When product prop changes (for edit), update formData
watch(() => props.product, (newProduct) => {
    Object.assign(formData, newProduct || defaultFormData);
}, { immediate: true });
</script>