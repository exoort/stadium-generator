<template>
  <q-page class="row items-center justify-evenly">
    <svg id="toBeParsed" width="832" height="630" viewBox="0 0 832 630" fill="none" xmlns="http://www.w3.org/2000/svg">
      <g id="Group 1">
        <circle id="json{'type': 'place', 'id': 1}" cx="274" cy="204" r="50" fill="#EA3333"/>
        <circle id="json{'type': 'place', 'id': 2}" cx="274" cy="377" r="50" fill="#EA3333"/>
        <circle id="json{'type': 'place', 'id': 3}" cx="416" cy="377" r="50" fill="#B08C8C"/>
        <circle id="json{'type': 'place', 'id': 4}" cx="558" cy="377" r="50" fill="#B08C8C"/>
        <circle id="json{'type': 'place', 'id': 5}" cx="416" cy="204" r="50" fill="#EA3333"/>
        <circle id="json{'type': 'place', 'id': 6}" cx="558" cy="204" r="50" fill="#EA3333"/>
      </g>
    </svg>
  </q-page>
</template>

<script setup lang="ts">
import { onMounted } from 'vue';

onMounted(() => {
  const svg = document.getElementById('toBeParsed');

  if (!svg) {
    return;
  }

  const parsed = svg.querySelectorAll('[id^="json"]');

  const resultArr: any[] = [];

  parsed.forEach((parsedElement) => {
    const result = JSON.parse(parsedElement.id.replace('json', '').replaceAll("'", '"'));

    parsedElement.id = `${result.type}-${result.id}`;

    parsedElement.dataset.type = result.type;
    parsedElement.dataset.id = result.id;
    parsedElement.dataset.selected = false;
    parsedElement.dataset.clickable = true;
    parsedElement.tabIndex = 0;
    parsedElement.dataset.disabled = false;

    resultArr.push(result);
  });

  document.getElementById('toBeParsed')?.addEventListener('click', (event) => {
    const nodeDataset = event.target?.dataset;
    if (nodeDataset && nodeDataset.clickable === 'true') {
      nodeDataset.selected = nodeDataset.selected === 'false';
      console.log('event', nodeDataset);
    }
  });
});
</script>

<style lang="scss">
svg {
  [data-clickable="true"] {
    cursor: pointer;

    &:hover {
      opacity: 0.7;
    }
  }

  [data-disabled="true"] {
    cursor: not-allowed;
    opacity: 0.2;
    pointer-events: none;
  }

  [data-selected="true"] {
    cursor: not-allowed;
    fill: green;
  }
}
</style>
