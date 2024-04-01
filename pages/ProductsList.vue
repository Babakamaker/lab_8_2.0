<script setup lang="ts">

const columns = [
  { key: 'title', label: 'Назва', sortable: true },
  { key: 'description', label: 'Опис', sortable: true },
  { key: 'price', label: 'Ціна', sortable: true },
  { key: 'rating', label: 'Оцінка', sortable: true },
  { key: 'brand', label: 'Бренд', sortable: true },
  { key: 'category', label: 'Категорія', sortable: true },
  { key: 'thumbnail', label: 'Фото' }
];
const {data, pending} = await useLazyAsyncData<any>(
    'products', () => $fetch('https://dummyjson.com/products'))

let products = data.value.products;

const page = ref(1)
const pageCount = 5

const q = ref('')
const rows = computed(() => {
  if (!q.value) {
    return products.slice((page.value - 1) * pageCount, (page.value) * pageCount)

  }

  return products.filter((product: any) => {
    return Object.values(product).some((value) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase())
    })
  })
})

const getRatingColor = (rating: number) => {
  return rating < 4.5 ? 'red' : 'green'
}
</script>

<template>
  <title>Список продуктів</title>
  <div class="">
    <div class="flex  px-3 py-3.5 border-b border-gray-200 dark:border-gray-700 ">
      <UInput v-model="q" placeholder="Filter products..." />
    </div>

    <UTable :rows="rows" :columns="columns" class="max-w-full h-auto flex-col flex">
      <template #description-data="{ row }">
        <span class=" overflow-x-auto max-w-[200px]">{{ row.description }}</span>
      </template>
      <template #rating-data="{ row }">
        <span :class="row.rating < 4.5 ? 'text-red-600' : 'text-green-600' ">{{ row.rating }}</span>
      </template>
      <template #thumbnail-data="{ row }">
        <img :src="row.thumbnail" alt="Thumbnail" class=" w-[100px] h-[100px]" />
      </template>

      </UTable>


    <UPagination v-model="page" :page-count="pageCount" :total="products.length" />
  </div>
</template>

