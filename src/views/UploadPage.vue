<template>
  <div class="video-uploader">
    <!-- <input type="file" @change="handleFileChange" ref="fileInput" /> -->

    <label class="block mb-2 text-sm font-medium text-gray-900 dark:text-white" for="file_input">Upload file</label>
    <input class="block w-full text-sm text-gray-900 border border-gray-300 rounded-lg cursor-pointer bg-gray-50 dark:text-gray-400 focus:outline-none dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400" id="file_input" type="file" @change="handleFileChange" ref="fileInput">

    <button type="button" class="text-white bg-blue-700 hover:bg-blue-800 focus:outline-none focus:ring-4 focus:ring-blue-300 font-medium rounded-full text-sm px-5 py-2.5 text-center me-2 mb-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800" @click="uploadVideo">Upload</button>
    <button type="button" class="text-white bg-red-700 hover:bg-red-800 focus:outline-none focus:ring-4 focus:ring-red-300 font-medium rounded-full text-sm px-5 py-2.5 text-center me-2 mb-2 dark:bg-red-600 dark:hover:bg-red-700 dark:focus:ring-red-900" @click="splitVideo">Split video</button>

  </div>

  <div class="gallery">
    <div v-for="(imageUrl, index) in splitFiles" :key="index" class="gallery-item">
      <img :src="'http://localhost:8080/out/' + imageUrl" :alt="'Image ' + index">
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
    };
  },
  methods: {
    handleFileChange(event) {
      this.selectedFile = event.target.files[0];
    },
    async splitVideo() {
      if (this.uploadedFileUuid) {
        try {
          const response = await fetch(`http://localhost:8080/pics/${this.uploadedFileUuid}`, {
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
          const response = await fetch('http://localhost:8080/upload', {
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
.video-uploader {
  @apply flex flex-col items-center;
}
input[type="file"] {
  @apply w-full mx-16; /* width: 100% minus 4rem margin */
}
button {
  @apply mt-8 text-lg font-semibold py-2 px-4 rounded-md bg-blue-500 text-white hover:bg-blue-700; /* Tlačítko je větší, se zaoblenými rohy a hover efektem */
}

.gallery {
  display: flex;
  flex-wrap: wrap;
  /* další styly podle potřeby */
}
.gallery-item {
  margin: 10px;
  /* další styly podle potřeby */
}
img {
  width: 100%;
  height: auto;
  /* další styly podle potřeby */
}
</style>

