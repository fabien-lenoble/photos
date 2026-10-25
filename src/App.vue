<script setup lang="ts">
async function uploadFile(file: File) {
  // 1. Obtenir l'URL d'upload depuis votre serveur
  const response = await fetch('/api/get-upload-url', {
    method: 'POST',
    body: JSON.stringify({ filename: file.name }),
  })

  if (!response.ok) {
    throw new Error("Impossible d'obtenir l'autorisation d'upload")
  }

  const { uploadUrl } = await response.json()

  // 2. Envoyer directement la photo à R2
  const uploadResponse = await fetch(uploadUrl, {
    method: 'PUT',
    headers: {
      'Content-Type': file.type,
    },
    body: file,
  })

  if (!uploadResponse.ok) {
    throw new Error(`Échec de l'upload : ${file.name}`)
  }

  return { filename: file.name, success: true }
}
</script>

<template>
  <p>Upload your photos here!</p>
  <input
    type="file"
    id="fileInput"
    @change="
      (e) => {
        const file = (e.target as HTMLInputElement).files?.[0]
        if (file) uploadFile(file)
      }
    "
  />
</template>

<style scoped></style>
