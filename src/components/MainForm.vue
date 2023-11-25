<script setup lang="ts">
import { ref } from "vue";
import Sparkles from "./Sparkles.vue";
import ScoreInput from "./ScoreInput.vue";

const scores = ref<{
  math: number;
  critical_reading: number;
  natural_science: number;
  social_science: number;
  english: number;
}>({
  math: 0,
  critical_reading: 0,
  natural_science: 0,
  social_science: 0,
  english: 0,
});

const prediction = ref<boolean | null>(null);
const probability = ref<number | null>(null);
const error = ref<boolean>(false)

const submit = () => {
  fetch(import.meta.env.VITE_FUNCTION_ENDPOINT, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "Authorization": `Bearer ${import.meta.env.VITE_AUTH_KEY}`
    },
    body: JSON.stringify(scores.value),
  })
    .then((res) => {
      return res.json();
    })
    .catch(_ => error.value = true)
    .then((data) => {
      const [f, t] = data.prediction
      prediction.value = t > f
      probability.value = prediction.value
        ? data.probability[1]
        : data.probability[0];
    });
};
</script>

<template>
  <div class="flex flex-col items-center gap-6">
    <div class="flex gap-4 w-full justify-between max-w-3xl mx-auto">
      <ScoreInput @change="prediction = null" v-model="scores.critical_reading" label="Lectura crítica" />
      <ScoreInput @change="prediction = null" v-model="scores.math" label="Matemáticas" />
      <ScoreInput @change="prediction = null" v-model="scores.social_science" label="Ciencias sociales" />
      <ScoreInput @change="prediction = null" v-model="scores.natural_science" label="Ciencias naturales" />
      <ScoreInput @change="prediction = null" v-model="scores.english" label="Inglés" />
    </div>
    <button type="submit"
      class="bg-indigo-500 hover:bg-indigo-400 text-white font-semibold py-2 px-4 rounded-md text-sm tracking-wide shadow-lg border-t-white/20 border-t flex items-center gap-1"
      @click="submit">
      <Sparkles class="h-4 w-4" />
      Pronosticar
    </button>
    <div v-show="error" class="text-center px-20 py-10 rounded-md bg-gray-50/50 border border-gray-300 drop-shadow-sm">
      Error obteniendo predicción, intente más tarde.
    </div>
    <div v-show="prediction != null"
      class="text-center px-20 py-10 rounded-md bg-gray-50/50 border border-gray-300 drop-shadow-sm">
      {{ prediction ? "" : "No" }} deberías estudiar una carrera STEM, con una probablidad de {{
        Number((probability ?? 0) * 100).toLocaleString("es-CO", {
          maximumFractionDigits: 2,
        })
      }}
      %.
    </div>
  </div>
</template>
