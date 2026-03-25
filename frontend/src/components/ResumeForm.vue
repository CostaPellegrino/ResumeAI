<script setup>
import { ref } from 'vue'

const emit = defineEmits(['close', 'save'])

const resumeTitle = ref('')
const resumeDescription = ref('')
const requiredSkills = ref('')

function saveresume() {
  if (!resumeTitle.value || !resumeDescription.value) return

  emit('save', {
    title: resumeTitle.value,
    description: resumeDescription.value,
    skills: requiredSkills.value
  })

  emit('close')
}
</script>

<template>
<div class="resume-form">

  <h3>Create resume Posting</h3>

  <input v-model="resumeTitle" placeholder="resume Title" class="form-input" />

  <textarea v-model="resumeDescription" placeholder="resume Description" class="form-input"></textarea>

  <input v-model="requiredSkills" placeholder="Required Skills (comma separated)" class="form-input" />

  <div class="form-buttons">
    <button @click="saveresume">Save</button>
    <button @click="emit('close')" class="cancel">Cancel</button>
  </div>

</div>
</template>