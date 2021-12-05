<template>
  <div id="detect" class="grey lighten-4 pa-4">
    <v-container class="container" grid-list-xs>
      <h1 class="text-center pb-5">"Nipa.Cloud Nvision's Object Detection"</h1>
      <upload-image
        @setupload-image="setUploadImage"
        v-if="uploadBtn === false"
      ></upload-image>
      <camera-image
        @setcamera-image="setCameraImage"
        @remove-image="removeImage"
        v-else
      ></camera-image>
      <section class="pt-4">
        <div class="d-flex justify-center">
          <button
            class="mr-5"
            :class="selectedUpload"
            @click="toggleBtn('upload')"
          >
            <v-icon small left>mdi-image-plus</v-icon> Upload Image
          </button>
          <button :class="selectedWebcam" @click="toggleBtn('webcam')">
            <v-icon small left>mdi-camera-wireless</v-icon> Webcam
          </button>
        </div>
        <div class="d-flex justify-center pt-4">
          <v-btn href="#result" outlined @click="detectImage">
            <v-icon small left>mdi-face-recognition</v-icon> Detect</v-btn
          >
        </div>
      </section>
      <section class="mt-8 white pa-5">
        <h1 class="text-center">Detection Result</h1>
        <v-divider class="mb-2"></v-divider>
        <div class="d-flex justify-center text-center pa-6">
          <div id="wrapper-image" class="wrapper-image">
            <img id="result" :src="imgCapture" />
          </div>
        </div>
      </section>
    </v-container>
  </div>
</template>

<script>
import axios from "axios";
import UploadImage from "@/components/UploadImage.vue";
import CameraImage from "@/components/CameraImage.vue";

export default {
  components: {
    UploadImage,
    CameraImage,
  },
  data() {
    return {
      uploadBtn: false,
      webcamBtn: true,
      imgCapture: null,
      imgBase64: null,
    };
  },
  computed: {
    selectedWebcam() {
      return this.webcamBtn ? "outline" : "active";
    },
    selectedUpload() {
      return this.uploadBtn ? "outline" : "active";
    },
  },
  methods: {
    toggleBtn(btn) {
      if (btn === "upload") {
        this.uploadBtn = false;
        this.webcamBtn = true;
        this.removeImage();
      }
      if (btn === "webcam") {
        this.webcamBtn = false;
        this.uploadBtn = true;
        this.removeImage();
      }
    },
    setUploadImage(imgUpload, imgUploadBase64) {
      this.removeImage();
      this.imgBase64 = imgUploadBase64;
      const result = document.getElementById("result");
      result.src = imgUpload;
    },
    setCameraImage(imgCapture, imgCaptureBase64) {
      this.imgBase64 = imgCaptureBase64;
      const result = document.getElementById("result");
      result.src = imgCapture;
    },
    removeImage() {
      const wrapperImage = document.getElementById("wrapper-image");
      wrapperImage.innerHTML = `<img id="result"/>`;
    },
    async detectImage() {
      try {
        const apiKey = process.env.VUE_APP_API_KEY;
        const res = await axios.post(
          `https://nvision.nipa.cloud/api/v1/object-detection`,
          {
            raw_data: this.imgBase64,
          },
          {
            headers: {
              Authorization: `ApiKey ${apiKey}`,
              "Content-Type": "application/json",
            },
          }
        );
        const wrapperImage = document.getElementById("wrapper-image");
        const objects = res.data.detected_objects;
        for (let i = 0; i < objects.length; i++) {
          const bottom = objects[i].bounding_box.bottom;
          const top = objects[i].bounding_box.top;
          const right = objects[i].bounding_box.right;
          const left = objects[i].bounding_box.left;
          const width = right - left;
          const height = bottom - top;
          wrapperImage.innerHTML += `
          <div 
            style="width: ${width}px; 
            height: ${height}px; 
            border: 5px solid #dce775d0; 
            position: absolute; 
            top: ${top}px; 
            bottom: ${bottom}px; 
            left: ${left}px; 
            right: ${right}px"
          >
            <div style="position: relative;">
              <div style="position: absolute; 
                top: -30px; 
                left: -5px;
                background: #dce775d0;
                padding: 2px;
                white-space: nowrap;"
              >
                ${objects[i].name} |
                ${objects[i].parent} | 
                ${(objects[i].confidence * 100).toFixed(2)}% 
              </div>
            </div>
          </div>`;
        }
      } catch (error) {
        alert("Sorry Somethings went wrong");
      }
    },
  },
};
</script>

<style scoped>
.container {
  padding-top: 75px;
}
button.outline {
  background: transparent;
  border: 1px solid #000;
  border-radius: 5px;
  padding: 0.3rem 0.5rem;
}

button.active {
  background: #f06292;
  color: #fff;
  border: 1px solid #000;
  border-radius: 5px;
  padding: 0.3rem 0.5rem;
}

button:active {
  background: rgba(0, 0, 0, 0.1);
  color: #000;
}
.wrapper-image {
  position: relative;
  overflow-x: auto;
}
</style>