<script setup lang="ts">

const columns = [
  { key: 'title', label: 'Назва' },
  { key: 'description', label: 'Опис' },
  { key: 'price', label: 'Ціна' },
  { key: 'rating', label: 'Оцінка' },
  { key: 'brand', label: 'Бренд' },
  { key: 'category', label: 'Категорія' },
  { key: 'thumbnail', label: 'Фото' }
];
const {data, pending} = await useLazyAsyncData<any>(
    'products', () => $fetch('https://dummyjson.com/products'))

let products = data.value.products;

const page = ref(1)
const pageCount = 8


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
  <div class="">
    <div class="flex  px-3 py-3.5 border-b border-gray-200 dark:border-gray-700 ">
      <UInput v-model="q" placeholder="Filter products..." />
    </div>

    <UTable :rows="rows" :columns="columns" class="">


      </UTable>


    <UPagination v-model="page" :page-count="pageCount" :total="products.length" />
  </div>
</template>

