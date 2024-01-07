<script setup>
import {ref} from 'vue'

defineProps({
  msg: String,
})

const count = ref(0)
</script>

<template>
  <h1>{{ msg }}</h1>

<!--  <div class="video-uploader">

    <label class="block mb-2 text-sm font-medium text-gray-900 dark:text-white" for="file_input">Upload file</label>
    <input class="block w-full text-sm text-gray-900 border border-gray-300 rounded-lg cursor-pointer bg-gray-50 dark:text-gray-400 focus:outline-none dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400" id="file_input" type="file" @change="handleFileChange" ref="fileInput">

    <button type="button" class="text-white bg-blue-700 hover:bg-blue-800 focus:outline-none focus:ring-4 focus:ring-blue-300 font-medium rounded-full text-sm px-5 py-2.5 text-center me-2 mb-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800" @click="uploadVideo">Upload video</button>
    <button type="button" class="text-white bg-red-700 hover:bg-red-800 focus:outline-none focus:ring-4 focus:ring-red-300 font-medium rounded-full text-sm px-5 py-2.5 text-center me-2 mb-2 dark:bg-red-600 dark:hover:bg-red-700 dark:focus:ring-red-900" @click="splitVideo">Split video</button>

  </div>-->
  <div class="video-uploader p-4 bg-gray-100 rounded-lg shadow-md my-20">

    <div class="mb-4">
      <label for="file_input" class="block mb-2 text-lg font-medium text-gray-700">Upload file:</label>
      <input id="file_input" type="file" @change="handleFileChange"
             class="block w-full text-sm text-gray-900 bg-white border border-gray-300 rounded-lg cursor-pointer focus:border-blue-500 focus:ring-blue-500 file:py-2 file:px-4 file:rounded-lg file:border-0 file:text-sm file:font-semibold file:bg-blue-50 file:text-blue-700 hover:file:bg-blue-100"/>
    </div>

    <div class="flex space-x-4">
      <button type="button" @click="uploadVideo"
              class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 focus:outline-none">
        Upload video
      </button>
      <button type="button" @click="splitVideo"
              class="text-white bg-red-700 hover:bg-red-800 focus:ring-4 focus:ring-red-300 font-medium rounded-lg text-sm px-5 py-2.5 focus:outline-none">
        Split Video
      </button>
    </div>

  </div>

  <div class="grid grid-cols-2 md:grid-cols-3 gap-4">
<!--    <div>-->
<!--      <img class="h-auto max-w-full rounded-lg" src="https://flowbite.s3.amazonaws.com/docs/gallery/square/image.jpg"-->
<!--           alt="">-->
<!--    </div>-->

    <div v-for="(imageUrl, index) in splitFiles" :key="index" class="gallery-item">
      <div>
          <img class="h-auto max-w-full rounded-lg"  :src="`${backendUrl}/out/` + imageUrl" :alt="'Image ' + index">
      </div>
    </div>

  </div>


</template>

<script>
export default {
  data() {
    return {
      selectedFile: null,
      uploadedFileUuid: null,
      splitFiles: null,
      backendUrl: null,
    };
  },
  created() {
    this.backendUrl = import.meta.env.VITE_BACKEND_URL;
  },
  methods: {
    handleFileChange(event) {
      this.selectedFile = event.target.files[0];
    },
    async splitVideo() {
      if (this.uploadedFileUuid) {
        try {
          const response = await fetch(`${this.backendUrl}/pics/${this.uploadedFileUuid}`, {
            method: 'POST',
          });

          if (response.ok) {
            console.log('Video bylo úspěšně rozděleno');
            const data = await response.json();
            this.splitFiles = data.files;
          } else {
            console.log('Nastala chyba při rozdělování videa');
          }
        } catch (error) {
          console.error('Došlo k chybě:', error);
        }
      }
    },
    async uploadVideo() {
      if (this.selectedFile) {
        const formData = new FormData();
        formData.append("file", this.selectedFile);

        try {
          const response = await fetch(`${this.backendUrl}/upload`, {
            method: 'POST',
            body: formData, // formData je zde posílána jako tělo požadavku
            // Nezapomeňte, že při použití FormData, není nutné nastavovat Content-Type header, to udělá browser automaticky
          });

          if (response.ok) {
            const data = await response.json(); // Získání JSON odpovědi
            this.uploadedFileUuid = data.uuid; // Uložení UUID do proměnné
            console.log('Video bylo úspěšně nahráno, UUID:', this.uploadedFileUuid);
          } else {
            console.log('Nastala chyba při nahrávání videa');
          }
        } catch (error) {
          console.error('Došlo k chybě:', error);
        }

        // Reset file input
        this.selectedFile = null;
        this.$refs.fileInput.value = "";
      }
    },
  },
};
</script>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
