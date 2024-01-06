<template>


  <div class="gallery">
    <div v-for="(imageUrl, index) in splitFiles" :key="index" class="gallery-item">
      <img :src="'${this.serverUrl}/out/' + imageUrl" :alt="'Image ' + index">
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
      backendUrl: null
    };
  },
  created() {
    // Access the SERVER_URL from the global window object
    this.backendUrl = window.env.BACKEND_URL;
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
          const response = await fetch('${this.serverUrl}/upload', {
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

