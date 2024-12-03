<template>
  <div class="upload-container">
    <h1 class="mb-3">खींच मेरी Photo !!!</h1>
    <img src="../src//assets/shubhashri.png" style="width: 50%" alt="">
    <form @submit.prevent="uploadPhotos">
          <label for="upload" class="file-upload-label">
            <input id="upload" type="file" ref="fileInput" multiple @change="displayFileNames" />
              <svg class="upload-icon-svg" fill="#000000" height="20px" width="20px" version="1.1" id="Capa_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" 
                    viewBox="0 0 471.2 471.2" xml:space="preserve">
              <g>
                  <g>
                      <path d="M457.7,230.15c-7.5,0-13.5,6-13.5,13.5v122.8c0,33.4-27.2,60.5-60.5,60.5H87.5c-33.4,0-60.5-27.2-60.5-60.5v-124.8
                          c0-7.5-6-13.5-13.5-13.5s-13.5,6-13.5,13.5v124.8c0,48.3,39.3,87.5,87.5,87.5h296.2c48.3,0,87.5-39.3,87.5-87.5v-122.8
                          C471.2,236.25,465.2,230.15,457.7,230.15z"/>
                      <path d="M159.3,126.15l62.8-62.8v273.9c0,7.5,6,13.5,13.5,13.5s13.5-6,13.5-13.5V63.35l62.8,62.8c2.6,2.6,6.1,4,9.5,4
                          c3.5,0,6.9-1.3,9.5-4c5.3-5.3,5.3-13.8,0-19.1l-85.8-85.8c-2.5-2.5-6-4-9.5-4c-3.6,0-7,1.4-9.5,4l-85.8,85.8
                          c-5.3,5.3-5.3,13.8,0,19.1C145.5,131.35,154.1,131.35,159.3,126.15z"/>
                  </g>
              </g>
              </svg>
              Select Files
          </label>
          <ol class="file-list" v-if="fileNames.length">
            <li v-for="(file, index) in fileNames" :key="index">
              <div class="file-list-wrapper">
                <div>
                  {{ file.name }}
                </div>
                <span class="delete-icon" @click="removeFile(index)">
                  <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" fill="#e65a5a" version="1.1" id="Capa_1" width="15px" height="15px" viewBox="0 0 482.428 482.429" xml:space="preserve">
                    <g>
                      <g>
                        <path d="M381.163,57.799h-75.094C302.323,25.316,274.686,0,241.214,0c-33.471,0-61.104,25.315-64.85,57.799h-75.098    c-30.39,0-55.111,24.728-55.111,55.117v2.828c0,23.223,14.46,43.1,34.83,51.199v260.369c0,30.39,24.724,55.117,55.112,55.117    h210.236c30.389,0,55.111-24.729,55.111-55.117V166.944c20.369-8.1,34.83-27.977,34.83-51.199v-2.828    C436.274,82.527,411.551,57.799,381.163,57.799z M241.214,26.139c19.037,0,34.927,13.645,38.443,31.66h-76.879    C206.293,39.783,222.184,26.139,241.214,26.139z M375.305,427.312c0,15.978-13,28.979-28.973,28.979H136.096    c-15.973,0-28.973-13.002-28.973-28.979V170.861h268.182V427.312z M410.135,115.744c0,15.978-13,28.979-28.973,28.979H101.266    c-15.973,0-28.973-13.001-28.973-28.979v-2.828c0-15.978,13-28.979,28.973-28.979h279.897c15.973,0,28.973,13.001,28.973,28.979    V115.744z"/>
                        <path d="M171.144,422.863c7.218,0,13.069-5.853,13.069-13.068V262.641c0-7.216-5.852-13.07-13.069-13.07    c-7.217,0-13.069,5.854-13.069,13.07v147.154C158.074,417.012,163.926,422.863,171.144,422.863z"/>
                        <path d="M241.214,422.863c7.218,0,13.07-5.853,13.07-13.068V262.641c0-7.216-5.854-13.07-13.07-13.07    c-7.217,0-13.069,5.854-13.069,13.07v147.154C228.145,417.012,233.996,422.863,241.214,422.863z"/>
                        <path d="M311.284,422.863c7.217,0,13.068-5.853,13.068-13.068V262.641c0-7.216-5.852-13.07-13.068-13.07    c-7.219,0-13.07,5.854-13.07,13.07v147.154C298.213,417.012,304.067,422.863,311.284,422.863z"/>
                      </g>
                    </g>
                  </svg>
                </span>
              </div>
            </li>
          </ol>

          <button type="submit" class="submit-btn">Upload !</button>
    </form>

    <div v-if="uploading" class="meter">
        <span style="width:100%;"><span class="progress"></span></span>
    </div>

    <div v-if="uploadedUrls.length > 0">
      <h3>Uploaded Images</h3>
      <div class="gallery">
        <img v-for="url in uploadedUrls" :key="url" :src="url" :alt="url" />
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      files: [],
      uploading: false,
      uploadedUrls: [],
      fileNames: [],
    };
  },
  methods: {
    displayFileNames(event) {
      const files = event.target.files;
      if (files.length > 0) {
        // Add the new selected files to the fileNames array
        this.fileNames = Array.from(files);
      } else {
        // If no files are selected, clear the list
        this.fileNames = [];
      }
    },
    removeFile(index) {
      // Remove the selected file from the list
      this.fileNames.splice(index, 1);
    },
    async uploadPhotos() {
      if (this.fileNames.length === 0) {
        alert('Please select files to upload!');
        return;
      }

      this.uploading = true;
      const uploadedUrls = [];

      for (let i = 0; i < this.fileNames.length; i++) {
        const file = this.fileNames[i];
        const formData = new FormData();
        formData.append('file', file);
        formData.append('upload_preset', 'wedding_photos_preset'); // Replace with your preset
        try {
          const response = await axios.post(
            'https://api.cloudinary.com/v1_1/dmtapqway/image/upload', // Replace with your Cloudinary cloud name
            formData
          );
          uploadedUrls.push(response.data.secure_url);
        } catch (error) {
          console.error('Error uploading file:', error);
        }
      }

      this.uploadedUrls = uploadedUrls;
      this.uploading = false;
    },
  },
};
</script>

<style scoped>
.upload-container {
  text-align: center;
  margin-top: 50px;
}

button {
  padding: 10px 20px;
  cursor: pointer;
}

.gallery img {
  width: 200px;
  margin: 10px;
}

.gallery {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

.shubhashri-img {
  width: 300px;
  height: 300px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 10px auto;
  border-radius: 50%;
  overflow: hidden;
  background-image: url('../src//assets/shubhashri.jpg');
  background-position: 35%;
  background-size: cover;
}
.meter { 
  margin-top: 20px;
    height: 8px;
    position: relative;
    background: #f3efe6;
    overflow: hidden;
}

.meter span {
    display: block;
    height: 100%;
}

.progress {
    background:linear-gradient(to right, rgb(173, 83, 137), rgb(60, 16, 83));
    animation: progressBar 3s ease-in-out;
    animation-fill-mode:both; 
}

@keyframes progressBar {
  0% { width: 0; }
  100% { width: 100%; }
}
</style>
