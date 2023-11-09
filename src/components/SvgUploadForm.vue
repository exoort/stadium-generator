<script setup lang="ts">
import { ref } from 'vue';

const emits = defineEmits<{(e: 'svgParsed', value: string): void;
}>();

const file = ref<File>();

function getFileContent(uploadedFile: File): Promise<string> {
  return new Promise((resolve) => {
    const reader = new FileReader();

    reader.addEventListener(
      "load",
      () => {
        resolve(reader.result as string);
      },
      false,
    );

    if (uploadedFile) {
      reader.readAsText(uploadedFile);
    }
  });
}

async function onSubmit() {
  if (file.value) {
    const result = await getFileContent(file.value);

    if (result && !result.startsWith('<svg')) {
      // TODO: show validation error here
      return;
    }

    emits("svgParsed", result);
  }
}
</script>

<template>
<q-form @submit.prevent="onSubmit">
  <q-file accept="image/svg+xml" clearable v-model="file" label="SVG file" />

  <div>
    <q-btn type="submit" label="Parse SVG" />
  </div>
</q-form>
</template>
