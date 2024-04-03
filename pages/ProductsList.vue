<script setup lang="ts">

const columns = [
  { key: 'title', label: 'Назва', sortable: true },
  { key: 'description', label: 'Опис', sortable: true },
  { key: 'price', label: 'Ціна', sortable: true },
  { key: 'rating', label: 'Оцінка', sortable: true },
  { key: 'brand', label: 'Бренд', sortable: true },
  { key: 'category', label: 'Категорія', sortable: true },
  { key: 'thumbnail', label: 'Фото', sortable: true}
];
const {data, pending} = await useLazyAsyncData<any>(
    'products', () => $fetch('https://dummyjson.com/products'))

let products = data.value.products;

const page = ref(1)
const pageCount = 5

const q = ref('')

const sort = ref({ column: 'title', direction: 'asc' as const })
const sortedRows = computed(() => {
  const sortedProducts = [...products]
  const { column, direction } = sort.value

  if (column && direction) {
    sortedProducts.sort((a, b) => {
      const aValue = a[column]
      const bValue = b[column]
      if (aValue < bValue) return direction === 'asc' ? -1 : 1
      if (aValue > bValue) return direction === 'asc' ? 1 : -1
      return 0
    })
  }
  console.log(sortedProducts)
  return sortedProducts
});
const rows = computed(() => {
  let filteredProducts = [...sortedRows.value]

  if (q.value) {
    filteredProducts = filteredProducts.filter(product => {
      return Object.values(product).some(value => {
        return String(value).toLowerCase().includes(q.value.toLowerCase())
      })
    })
  }
  const startIndex = (page.value - 1) * pageCount
  const endIndex = startIndex + pageCount
  return filteredProducts.slice(startIndex, endIndex)
})
const filteredRows = computed(() => {
  if (!q.value) {
    return products.slice((page.value - 1) * pageCount, (page.value) * pageCount)

  }

  return products.filter((product: any) => {
    return Object.values(product).some((value) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase())
    })
  })
})
</script>

<template>
  <title>Список продуктів</title>
  <div class="">
    <div class="flex  px-3 py-3.5 border-b border-gray-200 dark:border-gray-700 ">
      <UInput v-model="q" placeholder="Filter products..." />
    </div>

    <UTable class="w-full" v-model:sort="sort" sort-mode="manual" :rows="rows" :columns="columns">
      <template #description-data="{ row }">
        <span class=" overflow-x-auto max-w-[200px]">{{ row.description }}</span>
      </template>
      <template #rating-data="{ row }">
        <span :class="row.rating < 4.5 ? 'text-red-600' : 'text-green-600' ">{{ row.rating }}</span>
      </template>
      <template #thumbnail-data="{ row }">
        <img class="w-[100px] h-[100px]"  :src="row.thumbnail" alt="Thumbnail"/>
      </template>

      </UTable>


    <UPagination v-model="page" :page-count="pageCount" :total="products.length" />
  </div>
</template>

