<template>
  <q-page class="q-pa-md flex column flex-center q-gutter-lg">
    <div v-if="!ready">Loading....</div>

    <div v-if="ready">
      <!-- <input type="file" @change="(e) => getFiles(e.target.files?.item(0))" /> -->

      <q-file
        v-model="file"
        label="Pick one file"
        filled
        style="max-width: 800px"
        class="q-mb-sm"
        accept="video/*"
      />
      <q-btn
        color="primary"
        icon="upload"
        label="Upload"
        @click="getFiles(file)"
      />

      <q-video :src="url" style="max-width: 300px" />

      <div class="text-h5">GIF:</div>

      <q-img
        :src="gifURL"
        :ratio="16 / 9"
        spinner-color="primary"
        spinner-size="82px"
      />
    </div>
  </q-page>
</template>

<script lang="ts">
import { ref } from 'vue';
import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';

export default {
  setup() {
    const file = ref<File>();
    const ready = ref(false);
    const url = ref('');
    const gifURL = ref('');

    const ffmpeg = createFFmpeg({
      log: true,
    });

    async function load() {
      await ffmpeg.load();
      ready.value = true;
    }

    load();

    const gifConv = async (vid: File) => {
      ffmpeg.FS('writeFile', 'test.mp4', await fetchFile(vid));

      await ffmpeg.run(
        '-i',
        'test.mp4',
        '-t',
        '2.5',
        '-ss',
        '2.0',
        '-f',
        'gif',
        'out.gif'
      );

      const data = ffmpeg.FS('readFile', 'out.gif');

      const url = URL.createObjectURL(
        new Blob([data.buffer], { type: 'image/gif' })
      );

      gifURL.value = url;

      const link: HTMLAnchorElement = document.createElement('a');
      link.setAttribute('href', url);
      link.setAttribute('download', `${vid.name.split('.')[0]}.gif`);
      link.click();
    };

    const getFiles = (fileName: File) => {
      console.log(fileName);
      url.value = URL.createObjectURL(fileName);
      gifConv(fileName);
    };

    return {
      file,
      ready,
      getFiles,
      url,
      gifURL,
    };
  },
};
</script>
