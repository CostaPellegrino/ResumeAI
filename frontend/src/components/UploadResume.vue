<script setup>
import { ref, onMounted } from 'vue'

const resumes = ref([])
const fileInput = ref(null) // 👈 ADD THIS

// Load from localStorage
onMounted(() => {
  const saved = localStorage.getItem('resumes')
  if (saved) resumes.value = JSON.parse(saved)
})

// Save to localStorage
function saveToStorage() {
  localStorage.setItem('resumes', JSON.stringify(resumes.value))
}

// Handle file upload
function handleUpload(event) {
  const files = event.target.files

  for (let file of files) {
    const newResume = {
      name: file.name,
      size: (file.size / 1024).toFixed(0) + ' KB',
      date: new Date().toLocaleString()
    }

    resumes.value.unshift(newResume)
  }

  saveToStorage()
}


function removeResume(index) {
  resumes.value.splice(index, 1)
  saveToStorage()
}
</script>

<template>
<div class="content">

  <div class="header">
    <h1>Upload Resume</h1>
    <p>Upload candidate resumes for AI-powered screening</p>
  </div>

    <div class="upload-box" @click="fileInput.click()">

        <input 
            type="file" 
            multiple 
            ref="fileInput" 
            @change="handleUpload" 
        />

        <p>
            Drop your resume here, or 
            <span @click.stop="fileInput.click()">browse</span>
        </p>

        <small>Supports PDF, DOC, DOCX</small>

    </div>

  <div class="resume-list">
    <h3>Uploaded Resumes ({{ resumes.length }})</h3>

    <div
      v-for="(r, index) in resumes"
      :key="r.name"
      class="resume-item"
    >
      <div>
        <strong>{{ r.name }}</strong>
        <p>{{ r.size }} • {{ r.date }}</p>
      </div>

      <div class="actions">
        <span class="check">✔</span>
        <button @click="removeResume(index)">✖</button>
      </div>
    </div>

  </div>

</div>
</template>

<style scoped>
.upload-box {
  border: 2px dashed #ccc;
  border-radius: 12px;
  padding: 40px;
  text-align: center;
  background: white;
  margin-bottom: 20px;
  cursor: pointer;
}

.upload-box input {
  display: none;
}

.upload-box span {
  color: #3b82f6;
  cursor: pointer;
}

.resume-list {
  background: white;
  padding: 20px;
  border-radius: 12px;
}

.resume-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border-radius: 10px;
  background: #f9f9f9;
  margin-bottom: 10px;
}

.actions {
  display: flex;
  gap: 10px;
}

.check {
  color: green;
}

button {
  border: none;
  background: none;
  cursor: pointer;
}
</style>