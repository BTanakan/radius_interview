<template>
  <div>
    <div>{{ getLaunces.length }}</div>
    <div v-for="(lanuce, index) in getLaunces.length" :key="index">
      <div>Test</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { Launches } from 'src/models/launches';
import { defineProps } from 'vue';
import { ref, onMounted, Ref } from 'vue';
// const launces: Ref<Launches[]> = ref([]);
const props = defineProps({
  launces: {
    type: Array as () => Launches[],
    default: () => [],
  },
});
const getLaunces = () => {
  return props.launces;
};
</script>
