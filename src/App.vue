<script lang="ts" setup>
import { onMounted } from 'vue';
import { useUserStore } from './stores/user';
import { useSupabaseClient } from "../composables/supabase"

const userStore = useUserStore();
onMounted(async () => {
  const { data } = await useSupabaseClient.auth.getSession();
  if (data && data.session && data.session.user) {
    await userStore.insertProfile(data.session);
    userStore.setUserSession(data.session);
  }
  useSupabaseClient.auth.onAuthStateChange((_, _session) => {
    userStore.setUserSession(_session);
  });
});

</script>

<template>
  <router-view />
</template>

<!-- * We validate user session via supabase. On recieveing data we store in on the user store and use the store to upsert a profile in out database -->