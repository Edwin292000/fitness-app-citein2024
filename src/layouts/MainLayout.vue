<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="bg-green-9">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title> Fitness App </q-toolbar-title>

        <div>Fitness App{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered>
      <q-list>
        <q-item-label header> Essential Links </q-item-label>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script lang="ts">
import { useQuasar } from 'quasar';
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'MainLayout',
  setup() {
    const leftDrawerOpen = ref(false);
    const $q = useQuasar();

    return {
      leftDrawerOpen,
      $q,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
    };
  },
});
</script>
