<template>
  <div class="container mx-auto p-4">
    <UButton class="mb-4" to="/BlogPostAdd" label="Додати"></UButton>
    <UInput v-model="q" placeholder="Filter posts..." class="mb-4" />

    <div class="overflow-x-auto">
      <UTable :rows="filteredPosts" :columns="columns" class="w-full whitespace-nowrap bg-white rounded-lg shadow-lg mb-4">
        <template #user-name-data="{ row }">
          <span>{{ row.user.name }}</span>
        </template>
        <template #category-title-data="{ row }">
          <span>{{ row.category.title }}</span>
        </template>
        <template #title-data="{ row }">
          <router-link :to="`/BlogPost/${row.id}`">{{ row.title }}</router-link>
        </template>
        <template #published-at-data="{ row }">
          <span>{{ row.published_at }}</span>
        </template>

      </UTable>
    </div>

    <div class="flex justify-end">
      <UPagination v-model="page" :page-count="pageCount" :total="totalItems" />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useFetch } from '#app';

const columns = [
  { key: 'id', label: '#' },
  { key: 'user-name', label: 'Автор' },
  { key: 'category-title', label: 'Категорія' },
  { key: 'title', label: 'Заголовок' },
  { key: 'published-at', label: 'Дата публікації' },
  { key: 'actions', label: 'Дії' }
];

const page = ref(1);
const rowsPerPage = 10;
const q = ref('');
const totalItems = ref(0);
const posts = ref([]);

// Fetch posts from Laravel API
const fetchPosts = async () => {
  const { data, error } = await useFetch('http://127.0.0.1:8000/api/blog/posts');
  if (error.value) {
    console.error('Error fetching posts:', error.value);
    return;
  }
  posts.value = data.value || [];
  totalItems.value = posts.value.length;
};

const filteredPosts = computed(() => {
  if (!q.value) {
    return posts.value.slice((page.value - 1) * rowsPerPage, page.value * rowsPerPage);
  }
  return posts.value.filter((post) => {
    return Object.values(post).some((value) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase());
    });
  });
});

fetchPosts();
</script>

<style scoped>
/* Additional styling can be added here if needed */
</style>