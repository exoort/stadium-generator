<script setup lang="ts">
import { ref } from "vue";
import SvgUploadForm from "components/SvgUploadForm.vue";

const emits = defineEmits<{(e: "parsedSvg", svgContent: string): void;
}>();

const isOpened = ref(false);

const svgKey = ref(0);

const step = ref(1);

function setIsOpened(value: boolean) {
  isOpened.value = value;
  svgKey.value += 1;
  step.value = 1;
}

const parsedSvgContent = ref("");

function onParsedSvg(svgContent: string) {
  parsedSvgContent.value = svgContent;

  step.value = 2;
}

const defaultParser = (type: string) => {
  const elementId = `parsedSvg_${svgKey.value}`;
  const svg = document.getElementById(elementId);

  if (!svg) {
    throw new Error(`Could not find parsed svg element with id: ${elementId}`);
  }

  const parsed = svg.querySelectorAll(`[id^="type-${type}"]`);

  parsed.forEach((parsedElement) => {
    parsedElement.dataset.type = type;
    parsedElement.dataset.id = "";
    parsedElement.dataset.selected = false;
    parsedElement.dataset.clickable = true;
    parsedElement.tabIndex = 0;
    parsedElement.dataset.value = "";
    parsedElement.dataset.disabled = false;
  });

  return Promise.resolve(svg.outerHTML);
};

const parsers: Record<string, () => Promise<string>> = {
  sector: () => defaultParser("sector"),
  places: () => defaultParser("place"),
};

async function parseAs(parserName: string) {
  const parserFn = parsers[parserName];

  if (!parserFn) {
    return;
  }

  const result = await parserFn();

  emits("parsedSvg", result);

  setIsOpened(false);
}

</script>

<template>
  <slot v-if="$slots.activator" name="activator" :setIsOpened="setIsOpened"></slot>

  <q-dialog v-model="isOpened">
    <q-card :key="svgKey">
      <q-stepper
        v-model="step"
        ref="stepper"
        color="primary"
        animated
      >
        <q-step
          :name="1"
          title="Upload SVG"
          icon="upload"
          :done="step > 1"
        >
          <SvgUploadForm @svg-parsed="onParsedSvg" />
        </q-step>

        <q-step
          :name="2"
          title="Choose type"
          icon="settings"
          :done="step > 2"
        >
          <q-card-section>
            <q-btn @click="parseAs('sector')">Parse as sector</q-btn>

            <q-btn @click="parseAs('places')">Parse as places</q-btn>
          </q-card-section>

          <div v-if="parsedSvgContent" :id="`parsedSvg_${svgKey}`" v-html="parsedSvgContent"></div>
        </q-step>
      </q-stepper>
    </q-card>
  </q-dialog>
</template>
