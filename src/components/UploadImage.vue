<template>
  <section id="upload" class="pa-5 white">
    <h3 class="text-center">Upload Image</h3>
    <v-divider class="my-5"></v-divider>
    <div>
      <div class="wrapper-fileUpload">
        <label for="fileUpload">
          <img v-if="!imageUpload" src="../assets/img/upload-image.svg" />
          <img v-else :src="imageUpload" />
        </label>
      </div>
      <input
        id="fileUpload"
        type="file"
        accept="image/*"
        @change="onFileChange($event)"
      />
    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
      imageUpload: null,
      imageSelected: "",
      imgUploadConvertBase64: null,
    };
  },

  methods: {
    onFileChange(e) {
      let files = e.target.files;
      this.createImage(files[0]);
    },
    createImage(files) {
      let reader = new FileReader();
      reader.readAsDataURL(files);
      reader.onload = (e) => {
        this.imageUpload = e.target.result;
        const imgUploadSplit = this.imageUpload.split(",");
        this.imgUploadConvertBase64 = imgUploadSplit[1];
        this.$emit(
          "setupload-image",
          this.imageUpload,
          this.imgUploadConvertBase64
        );
      };
    },
  },
};
</script>

<style scoped>
#upload {
  height: 100%;
}
.wrapper-fileUpload {
  width: 60%;
  position: relative;
  transform: translateX(-50%);
  right: -50%;
  border: 5px dashed #f8bbd0;
  padding: 1.5rem;
}

label img {
  width: 100%;
  cursor: pointer;
}

input[type="file"]#fileUpload {
  display: none;
  height: 500px;
  width: 100%;
  cursor: pointer;
}
input[type="file"]#fileUpload::file-selector-button {
  display: none;
}
</style>