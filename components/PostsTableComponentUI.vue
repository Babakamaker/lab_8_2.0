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
        <template #actions-data="{ row }">
          <UDropdown :items="items(row)">
            <UButton color="gray" variant="ghost" icon="i-heroicons-ellipsis-horizontal-20-solid"/>
          </UDropdown>
        </template>
      </UTable>
    </div>

    <div class="flex justify-end">
      <UPagination v-model="page" :page-count="pageCount" :total="totalItems"/>
    </div>
  </div>
</template>

<script setup>
import {ref, computed, onMounted} from 'vue';
import {useFetch} from '#app';
import {UButton, UInput, UTable, UDropdown, UPagination} from '@nuxt/ui';

const columns = [
  {key: 'id', label: '#'},
  {key: 'user-name', label: 'Автор'},
  {key: 'category-title', label: 'Категорія'},
  {key: 'title', label: 'Заголовок'},
  {key: 'published-at', label: 'Дата публікації'},
  {key: 'actions', label: 'Дії'}
];

const page = ref(1);
const rowsPerPage = 10;
const q = ref('');
const totalItems = ref(0);
const posts = ref([]);

const fetchPosts = async () => {
  const {data, error} = await useFetch('http://127.0.0.1:8000/api/blog/posts');
  if (error.value) {
    console.error('Error fetching posts:', error.value);
    return;
  }
  posts.value = data.value || [];
  totalItems.value = posts.value.length;
};

const filteredPosts = computed(() => {
  let filtered = posts.value;

  if (q.value) {
    filtered = filtered.filter((post) => {
      return Object.values(post).some((value) => {
        return String(value).toLowerCase().includes(q.value.toLowerCase());
      });
    });
  }

  const start = (page.value - 1) * rowsPerPage;
  const end = start + rowsPerPage;
  return filtered.slice(start, end);
});

const pageCount = computed(() => {
  return Math.ceil(totalItems.value / rowsPerPage);
});

const goToAddPostPage = () => {
  window.location.href = '/admin/blog/posts/create';
};

const editPost = (id) => {
  window.location.href = `/BlogPostEdit/${id}`;
};

const deletePost = async (id) => {
  try {
    const response = await fetch(`http://127.0.0.1:8000/api/blog/posts/${id}`, {
      method: 'DELETE'
    });
    if (!response.ok) {
      throw new Error('Failed to delete post');
    }
    await fetchPosts();
  } catch (error) {
    console.error('Error deleting post:', error);
  }
};

const items = (row) => [
  [{
    label: 'Редагувати',
    icon: 'i-heroicons-pencil-square-20-solid',
    click: () => editPost(row.id)
  }],
  [{
    label: 'Видалити',
    icon: 'i-heroicons-trash-20-solid',
    click: () => deletePost(row.id)
  }]
];

onMounted(fetchPosts);
</script>

<style scoped>
/* Additional styling can be added here if needed */
</style>
