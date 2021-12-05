<template>
  <section id="camera" class="pa-5 white">
    <h3 class="text-center">Camera Image</h3>
    <v-divider class="my-5"></v-divider>
    <div>
      <div class="wrapper-webcam d-flex justify-center">
        <web-cam
          v-if="!imageCapture"
          :deviceId="deviceId"
          @cameras="onCameras"
          @camera-change="onCameraChange"
          ref="webcam"
          width="100%"
        ></web-cam>
        <img :ref="'imageCapturePreview'" class="imageCapture" />
      </div>
    </div>
    <div class="d-flex justify-center mt-4">
      <v-btn
        class="text-center mr-3"
        outlined
        color="orange darken-1"
        @click="onCapture"
      >
        <v-icon small left>mdi-camera-iris</v-icon> capture
      </v-btn>
      <v-btn
        class="text-center"
        outlined
        color="red darken-1"
        @click="removeImage"
      >
        <v-icon small left>mdi-camera-iris</v-icon> capture again!
      </v-btn>
    </div>
  </section>
</template>

<script>
import { WebCam } from "vue-web-cam";

export default {
  components: {
    WebCam,
  },
  data() {
    return {
      deviceId: null,
      devices: [],
      imageCapture: null,
      imgCaptureConvertBase64: null,
    };
  },
  watch: {
    devices() {
      if (this.devices[0]) {
        this.deviceId = this.devices[0].deviceId;
      }
    },
  },
  methods: {
    onCameras(camera) {
      this.devices = camera;
    },
    onCameraChange(deviceId) {
      this.deviceId = deviceId;
    },
    onCapture() {
      this.imageCapture = this.$refs.webcam.capture();
      this.$refs.imageCapturePreview.src = this.imageCapture;
      const imageCaptureSplit = this.imageCapture.split(",");
      this.imgCaptureConvertBase64 = imageCaptureSplit[1];

      this.$emit('setcamera-image', this.imageCapture, this.imgCaptureConvertBase64)
    },
    removeImage() {
      this.imageCapture = null;
      this.imgCaptureConvertBase64 = null;
      this.deviceId = null;
      this.devices = [];
      this.$refs.imageCapturePreview.src = "";
      this.onCameraChange();

      this.$emit('remove-image')
    },
  },
};
</script>

<style scoped>
.wrapper-webcam {
  width: 60%;
  border: 5px dashed #f8bbd0;
  transform: translateX(-50%);
  position: relative;
  right: -50%;
  padding: 0rem 2rem;
  display: flex;
  align-items: center;
}

img {
  width: 100%;
  padding: 1.5rem 0rem;
}
</style>