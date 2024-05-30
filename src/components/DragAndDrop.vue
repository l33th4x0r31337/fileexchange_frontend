<script setup>
import { onMounted, onUnmounted } from 'vue'
import { ref } from 'vue'
import axios from 'axios'

axios.defaults.baseURL = 'http://localhost:8080'

const fileUrl = ref(null)
const numOfFiles = ref(0)

function onDrop(e) {
  numOfFiles.value = e.dataTransfer.files.length
  uploadFiles([...e.dataTransfer.files])
}

function onChange(e) {
  uploadFiles([...e.target.files])
}

function uploadFiles(files) {
  const formData = new FormData()
  files.forEach((file) => formData.append('files', file))

  axios
    .post('/api/files/upload', formData, {
      headers: {
        'Content-Type': 'multipart/form-data'
      }
    })
    .then((response) => {
      fileUrl.value = `${axios.defaults.baseURL}/api/files/${response.data.id}`
    })
}

function preventDefaults(e) {
  e.preventDefault()
}

const events = ['dragenter', 'dragover', 'dragleave', 'drop']

onMounted(() => {
  events.forEach((event) => {
    document.body.addEventListener(event, preventDefaults)
  })
})

onUnmounted(() => {
  events.forEach((event) => {
    document.body.removeEventListener(event, preventDefaults)
  })
})
</script>

<template>
  <div id="dragndrop">
    <h1>Drag & Drop Files</h1>
    <label id="dropzone" @drop.prevent="onDrop" for="file-input">
      {{ numOfFiles > 0 ? `${numOfFiles} file(s) chosen` : 'Drop Here' }}
      <input type="file" id="file-input" multiple @change="onChange" />
    </label>
    <a v-if="fileUrl" :href="fileUrl" target="_blank" id="upload-result">{{ fileUrl }}</a>
  </div>
</template>

<style scoped>
#dragndrop {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: Verdana, Geneva, Tahoma, sans-serif;
}

#dropzone {
  width: 80%;
  height: 600px;
  border: 3px solid black;
  border-radius: 6px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}

#upload-result {
  margin-top: 20px;
}

#file-input {
  display: none;
}
</style>
