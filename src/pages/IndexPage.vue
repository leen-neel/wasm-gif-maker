<template>
  <q-page class="q-pa-md">
    <q-file
      v-model="file"
      label="Pick one file"
      filled
      style="max-width: 300px"
      accept="video/*"
      class="q-mb-sm"
    />
    <q-btn color="primary" icon="upload" label="Upload" @click="getFiles" />
    {{ ready }}
  </q-page>
</template>

<script lang="ts">
import { ref } from 'vue';
import { createFFmpeg } from '@ffmpeg/ffmpeg';

export default {
  setup() {
    const file = ref<FileList>();
    const ready = ref(false);

    const ffmpeg = createFFmpeg({
      log: true,
    });

    async function load() {
      await ffmpeg.load();
      ready.value = true;
    }

    load();

    const getFiles = () => {
      console.log(file.value);
    };

    return {
      file,
      ready,
      getFiles,
    };
  },
};
</script>
