<script setup lang="ts">
import { onMounted, ref } from "vue";

import SvgUploadDialog from "components/SvgUploadDialog.vue";

const parsedSvg = ref("");

function onParsedSvg(svgContent: string) {
  parsedSvg.value = svgContent;
}

onMounted(() => {
  document.getElementById('svgContent')?.addEventListener('click', (event) => {
    const nodeDataset = event.target?.dataset;
    if (nodeDataset && nodeDataset.clickable === 'true') {
      nodeDataset.selected = nodeDataset.selected === 'false';
      console.log('event', nodeDataset);
    }
  });
});
</script>

<template>
  <q-page class="row">
    <SvgUploadDialog @parsed-svg="onParsedSvg">
      <template #activator="{ setIsOpened }">
        <q-btn @click="setIsOpened(true)">Upload SVG</q-btn>
      </template>
    </SvgUploadDialog>

    <q-tooltip
      v-if="parsedSvg"
      :model-value="true"
      target="#type-place"
      class="bg-black"
    >Quasar Rulz!</q-tooltip>

    <div v-if="parsedSvg" id="svgContent" v-html="parsedSvg"></div>

  </q-page>
</template>

<style lang="scss">
svg {
  [data-clickable="true"] {
    cursor: pointer;

    &:hover {
      opacity: 0.7;
    }
  }

  //[data-id=""] {
  //  position: relative;
  //
  //  &::after {
  //    content: "No value";
  //    position: absolute;
  //    display: block;
  //    top: -20px;
  //    border:1px solid #000;
  //    width:40px;
  //    height:40px;
  //    margin:5px auto;
  //    background-color:#DDD;
  //  }
  //}
}
</style>
