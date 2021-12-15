<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <Questions/>
  <h1>{{title}}</h1>
  <div v-if="questionsState">
    <form action="#">
      <div class="shadow overflow-hidden sm:rounded-md">
        <div class="px-4 py-5 bg-white space-y-6 sm:p-6">
          <fieldset v-for="(question, index) in questionsState" :key="index" :id="'Q'+index">
            <div>
              <legend class="text-base font-medium text-gray-900">{{question.question}}</legend>
            </div>
            <div class="mt-4 space-y-4">
              <div class="flex items-center" v-for="(choice, i) in choices[index]" :key="i">
                <input :id="'Q' + index + i" :name="'Q' + index" type="radio" :value="choice" v-model="answers[index]" class="focus:ring-indigo-500 h-4 w-4 text-indigo-600 border-gray-300"/>
                <label :for="'Q' + index + i" class="ml-3 block text-sm font-medium text-gray-700">
                  {{choice}}
                </label>
              </div>
            </div>
          </fieldset>
        </div>
        <div class="px-4 py-3 bg-gray-50 text-right sm:px-6">
          <button type="submit" class="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
            Save
          </button>
        </div>
      </div>
    </form>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted, computed } from "vue";
import Questions from "./components/Questions.vue";
import {questions as Q, title} from '@/assets/questions.json';

const questionsState = ref([] as any)
const titleState = ref('');
const answers = ref([] as any)

const choices = computed(() => {
  return questionsState.value.map((question: any) => {
    return question.choices;
  })
})


onMounted(() => {
  questionsState.value = Q
  titleState.value = title;
  questionsState.value.forEach(question => {
    answers.value.push('');
  });
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
