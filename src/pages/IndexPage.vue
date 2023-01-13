<template>
  <q-page class="column q-pa-md">
    <q-file
      v-model="file"
      label="Pick one file"
      filled
      style="max-width: 300px"
      accept="video/*"
    />

    {{ loading }}
    {{ file }}
  </q-page>
</template>

<script lang="ts">
import { ref } from 'vue';
import { createFFmpeg } from '@ffmpeg/ffmpeg';

export default {
  setup() {
    const file = ref<FileList>();
    const loading = ref(true);

    const ffmpeg = createFFmpeg({ log: true });

    const load = async () => {
      await ffmpeg.load();
      loading.value = false;
    };

    load();

    return {
      file,
      loading,
    };
  },
};
</script>
