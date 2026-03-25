<script setup>
import { ref } from 'vue'

const emit = defineEmits(['close', 'save'])

const jobTitle = ref('')
const jobDescription = ref('')
const requiredSkills = ref('')

function saveJob() {
  if (!jobTitle.value || !jobDescription.value) return

  emit('save', {
    title: jobTitle.value,
    description: jobDescription.value,
    skills: requiredSkills.value
  })

  emit('close')
}
</script>

<template>
<div class="job-form">

  <h3>Create Job Posting</h3>

  <input v-model="jobTitle" placeholder="Job Title" class="form-input" />

  <textarea v-model="jobDescription" placeholder="Job Description" class="form-input"></textarea>

  <input v-model="requiredSkills" placeholder="Required Skills (comma separated)" class="form-input" />

  <div class="form-buttons">
    <button @click="saveJob">Save</button>
    <button @click="emit('close')" class="cancel">Cancel</button>
  </div>

</div>
</template>