<script setup lang="ts">
import { computed } from "vue";

const props = defineProps(["modelValue", "label"]);
const emit = defineEmits(["update:modelValue"]);

const value = computed({
  get() {
    return props.modelValue;
  },
  set(value) {
    if (value >= 0 && value <= 100) emit("update:modelValue", value);
    else emit("update:modelValue", 0);
  },
});
</script>

<template>
  <div class="flex flex-col items-center gap-2 justify-stretch text-center">
    <label class="text-sm font-medium tracking-wide">{{ props.label }}</label>
    <input
      class="h-24 w-24 text-center text-5xl rounded-lg font-light ring-inset ring-gray-100 ring-1 outline-none focus:ring-inset focus:ring-indigo-600 tracking-tight drop-shadow-sm"
      type="number"
      max="100"
      min="0"
      v-model="value"
      required
    />
  </div>
</template>

<style scoped>
/* Chrome, Safari, Edge, Opera */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type="number"] {
  -moz-appearance: textfield;
}
</style>
