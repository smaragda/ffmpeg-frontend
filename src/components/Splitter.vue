<template>
  <div class="video-uploader">
    <button @click="splitVideo">Split</button>
  </div>
</template>


<script>
export default {
  data() {
    return {
      splitFiles: null,
      backendUrl: null
    };
  },
  created() {
    // Access the SERVER_URL from the global window object
    this.backendUrl = window.env.BACKEND_URL;
  },
  methods: {
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
  },
};
</script>


<style scoped>
button {
  @apply mt-8 text-lg font-semibold py-2 px-4 rounded-md bg-blue-500 text-white hover:bg-blue-700; /* Tlačítko je větší, se zaoblenými rohy a hover efektem */
}
</style>

