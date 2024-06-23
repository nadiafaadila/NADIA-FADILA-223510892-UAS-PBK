<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated class="bg-gradient text-white">
      <q-toolbar>
        <q-btn dense flat round icon="menu" @click="toggleLeftDrawer" />

        <q-toolbar-title>
          NADIA FADILA - 223510892
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer show-if-above v-model="leftDrawerOpen" side="left" bordered>
      <q-tabs>
        <q-route-tab to="/Todo" label="Todolist" />
        <q-route-tab to="/Post" label="Post" />
        <q-route-tab to="/Album" label="Gallery" />
      </q-tabs>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { ref } from "vue";

export default {
  setup() {
    const leftDrawerOpen = ref(false);

    return {
      leftDrawerOpen,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
    };
  },
};
</script>

<style scoped>
.q-header {
  background-color: #ff9a9e; /* Primary color */
}

.bg-gradient {
  background: linear-gradient(135deg, #ff9a9e, #fecfef); /* Gradient background */
}

.q-toolbar-title {
  color: white; /* Text color */
}
</style>
