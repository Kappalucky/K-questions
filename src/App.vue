<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <Questions/>
  <h1>{{title}}</h1>
  <div v-if="test">
    <ol v-for="(question, index) in test" :key="index">
    {{question.question}}
    <li v-for="(choice, i) in choices[index]" :key="i">
    {{choice}}
    </li>
    </ol>
    <button>Submit</button>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted, computed } from "vue";
import Questions from "./components/Questions.vue";
import questions from '@/assets/questions.json';

const test = ref([] as any)
const title = ref('');

const choices = computed(() => {
  return test.value.map((question: any) => {
    return question.choices;
  })
})


onMounted(() => {
  test.value = questions.questions
  title.value = questions.title;
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
