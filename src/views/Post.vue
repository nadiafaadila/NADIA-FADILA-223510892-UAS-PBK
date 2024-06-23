<template>
  <div class="post-container">
    <div class="form-group">
      <div class="text-h4">Postingan</div>
      <label for="userSelect">Select User:</label>
      <select id="userSelect" v-model="selectedUser" class="form-control">
        <option v-for="user in users" :key="user.id" :value="user.id">
          {{ user.name }}
        </option>
      </select>
    </div>

    <ul class="list-group mt-3" v-if="selectedUser">
      <li class="list-group-item" v-for="post in userPosts" :key="post.id">
        <h4 class="post-title">{{ post.title }}</h4>
        <p>{{ post.body }}</p>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from "vue";

const users = ref([]);
const selectedUser = ref(null);
const userPosts = ref([]);

const fetchUsers = async () => {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");
    users.value = await response.json();
  } catch (error) {
    console.error("Error fetching users:", error);
  }
};

const fetchUserPosts = async (userId) => {
  try {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/posts?userId=${userId}`
    );
    userPosts.value = await response.json();
  } catch (error) {
    console.error(`Error fetching posts for user ${userId}:`, error);
  }
};

watch(selectedUser, (newVal) => {
  if (newVal) {
    fetchUserPosts(newVal);
  }
});

onMounted(fetchUsers);
</script>

<style scoped>
.post-container {
  background: linear-gradient(135deg, #ff9a9e 0%, #fecfef 100%);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 20px;
}

.text-h4 {
  font-size: 1.5rem;
  font-weight: bold;
  color: #333;
  margin-bottom: 10px;
}

.form-control {
  width: 100%;
  padding: 10px;
  font-size: 1rem;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.list-group-item {
  background-color: #fff;
  border: 1px solid #ddd;
  margin-bottom: 10px;
  padding: 15px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.post-title {
  font-size: 1.2rem;
  color: #ff5bc5;
  margin-bottom: 5px;
}

.list-group-item p {
  color: #666;
  line-height: 1.6;
}
</style>
