<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <Questions/>
  <h1>{{titleState}}</h1>
  <div v-if="questionsState">
    <form @submit.prevent="handleSubmit">
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
            Submit
          </button>
        </div>
      </div>
    </form>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted, computed, unref } from "vue";
import Questions from "./components/Questions.vue";
import {questions as Q, title} from '@/assets/questions.json';

const questionsState = ref([] as any)
const titleState = ref(title);
const answers = ref([] as any)
const unanswered = ref([] as any)

const choices = computed(() => {
  return questionsState.value.map((question: any) => {
    return question.choices;
  })
})

const handleSubmit = () => {
  //?: No need to do any array validation as the answers array is dynamically filled to the length of the state
  //TODO: If any of the answers in the answers array is blank, add error class to panel relating to index
  //TODO: If everything is good, call grading function
  const errorCheck = [] as any;

  for (let index = 0; index < unref(answers).length; index++) {
    const element = unref(answers)[index];
    if (element === '') {
      errorCheck.push(index);
    }
  }

  if (errorCheck.length != 0) {
    console.log(errorCheck)
    console.log('Answer all questions before submitting. Unanswered questions are displayed in yellow.')
  } else {
    console.log('sent');
  }
}

const grading = () => {
  //TODO: Need to check each answer in answers array against questionState.answer based on index
  //TODO: If values do not match, add error class and messages to panel
  //TODO: Highlight correct answer based on index of question
  //TODO: If correct, add success class and message to panel
}


onMounted(() => {
  questionsState.value = Q
  //titleState.value = title;
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
