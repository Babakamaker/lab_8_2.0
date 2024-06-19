<template>
  <div class="container">
    <div class="flex justify-center">
      <div class="w-full">
        <nav class="navbar bg-gray-600">
          <a href="/admin/blog/posts/create">Додати</a>
        </nav>
        <div class="card">
          <div class="card-body">
            <table class="table table-auto">
              <thead>
              <tr>
                <th>#</th>
                <th>Автор</th>
                <th>Категорія</th>
                <th>Заголовок</th>
                <th>Дата публікації</th>
              </tr>
              </thead>
              <tbody>
              <tr v-for="post in posts" :key="post.id">
                <td>{{ post.id }}</td>
                <td>{{ post.user?.name || 'Unknown' }}</td>
                <td>{{ post.category?.title || 'Unknown' }}</td>
                <td><a :href="'/admin/blog/posts/' + post.id + '/edit'">{{ post.title }}</a></td>
                <td>{{ post.published_at || 'Unknown' }}</td>
              </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

interface Post {
  id: number;
  title: string;
  published_at?: string;
  user?: {
    name: string;
  };
  category?: {
    title: string;
  };
}

const posts = ref<Post[]>([]); // Initialize posts as a ref with an empty array

const getPosts = async () => {
  try {
    const response = await fetch('http://localhost:8000/api/blog/posts');
    const data = await response.json();
    console.log(data);
    posts.value = data; // Update the posts array with fetched data
  } catch (error) {
    console.error('Error fetching posts:', error);
  }
};

onMounted(getPosts); // Call getPosts function when the component is mounted
</script>

<style scoped>
/* Add your scoped styles here */
.container {
  padding: 1rem;
}
.navbar {
  margin-bottom: 1rem;
}
.card {
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
  overflow: hidden;
}
.card-body {
  padding: 1rem;
}
.table {
  width: 100%;
  border-collapse: collapse;
}
.table th, .table td {
  padding: 0.5rem;
  border-bottom: 1px solid #e5e7eb;
}
</style>
