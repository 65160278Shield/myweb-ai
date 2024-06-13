<script setup>
import { ref } from 'vue'
import http from '../services/http'
import { useLoginStore } from '../stores/login.js'

const fileInput = ref(null)
const uploadStatus = ref('')
const loginStore = useLoginStore()

async function uploadFile() {
  const file = fileInput.value.files[0]
  if (!file) {
    uploadStatus.value = 'No file selected'
    return
  }

  uploadStatus.value = 'Uploading...'
  const formData = new FormData()
  formData.append('file', file)

  try {
    const res = await http.post('/auth/uploads', formData, {
      headers: {
        'Content-Type': 'multipart/form-data'
      }
    })
    console.log(res.data)
    loginStore.updateLogin()
    uploadStatus.value = 'Upload successful!'
  } catch (err) {
    console.log(err)
    uploadStatus.value = 'Upload failed. Please try again.'
  }
}
</script>

<template>
  <div class="upload-container">
    <div class="profile-image">
      <img
        :src="'http://localhost:3000/uploads/' + loginStore.currentUser.image"
        alt="Profile image"
        class="profile-pic"
      />
    </div>
    <form @submit.prevent="uploadFile" class="upload-form">
      <input type="file" ref="fileInput" class="file-input"/>
      <button type="submit" class="upload-button">Upload</button>
    </form>
    <div class="status-message">{{ uploadStatus }}</div>
    <div class="user-info">{{ loginStore.currentUser }}</div>
  </div>
</template>

<style scoped>
.upload-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  background: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.profile-image {
  margin-bottom: 20px;
}

.profile-pic {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  object-fit: cover;
  border: 2px solid #ddd;
}

.upload-form {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.file-input {
  margin-bottom: 10px;
}

.upload-button {
  background-color: #007bff;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.upload-button:hover {
  background-color: #0056b3;
}

.status-message {
  margin-top: 10px;
  font-size: 14px;
  color: #555;
}

.user-info {
  margin-top: 20px;
  font-size: 16px;
  color: #333;
}
</style>
