<template>
<div class="min-h-full mt-6">
  <!--<Questions/>-->
  <h1 class="text-2xl font-semibold">{{titleState}}</h1>
  <template v-if="results.number !== null">
    <h4>Results</h4>
    <p>You got {{results.number}} / {{questionsState.length}} questions correct</p>
    <p>{{results.percentage}}<span>&nbsp;%</span></p>
  </template>
  <div v-if="questionsState">
    <form @submit.prevent="handleSubmit">
      <div class="shadow overflow-hidden sm:rounded-md">
        <div class="px-4 py-5 bg-white space-y-6 sm:p-6">
          <fieldset
            v-for="(question, index) in questionsState"
            :key="index" :id="'Q'+index"
            class="relative px-6 pt-10 pb-8 bg-white shadow-xl ring-2 xs:max-w-lg md:max-w-full sm:mx-auto rounded-lg sm:px-10"
            :class="[answers[index] !== '' ? 'ring-gray-900/5' : '']"
            >
            <div>
              <legend class="text-left font-medium text-gray-900"><span>{{index + 1}}.&nbsp;</span>{{question.question}}</legend>
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
        <p v-if="pageError.active">{{pageError.message}}</p>
        <div class="px-4 py-3 text-center sm:px-6 text-base leading-7 font-semibold">
          <button
          type="submit"
          :disabled="results.number !== null"
          class="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          :class="[results.number !== null ? 'bg-gray-600 hover:bg-gray-700' : 'bg-indigo-600 hover:bg-indigo-700']"
          >
            Submit
          </button>
        </div>
      </div>
    </form>
  </div>
</div>
</template>

<script setup lang="ts">
//* System imports
import { ref, reactive, onMounted, computed, unref } from "vue";
import {questions as Q, title} from '@/assets/questions.json';

//* Local components
import Questions from "./components/Questions.vue";

//* Initialize variables & functions
const questionsState = ref(Q as any)
const titleState = ref(title);
const answers = ref([] as any)
const unanswered = ref([] as any)
const pageError = reactive({
  message: '',
  active: false
});
const results = reactive({
  number: null as null | number,
  percentage: null as null | number
})

const choices = computed(() => {
  return unref(questionsState).map((question: any) => {
    return question.choices;
  })
})

const handleSubmit = () => {
  /* If answer relating to position of answers array is empty, add error and styling */

  //* Clears unanswered array on every call before checking answers to avoid joining multiple calls
  unanswered.value = [];

  for (let index = 0; index < unref(answers).length; index++) {
    const answer = unref(answers)[index];
    if (answer === '') {
      unref(unanswered).push(index);
    }
  }

  if (unref(unanswered).length != 0) {
    //* For every index within unanswered array, add error styling to appropriate html field
    unref(unanswered).forEach((index: number) => {
      const itemCheck = document.getElementById(`Q${index}`);
      itemCheck ? itemCheck.classList.add('ring-yellow-400') : '';
    });

    pageError.message = 'Answer all questions before submitting. Unanswered questions are displayed in yellow.';
    pageError.active = true;

  } else {
    pageError.message = '';
    pageError.active = false;
    grading();
  }
}

const grading = () => {
  /* Each answer in answers array is checked against questionState.answer. Values are calculated based on returned count */
  //TODO: If values do not match, add error class and messages to panel
  //TODO: Highlight correct answer based on index of question
  //TODO: If correct, add success class and message to panel
  let rightAnswerCount = 0;

  for (let index = 0; index < unref(answers).length; index++) {
    const answer = unref(answers)[index];
    if (answer === unref(questionsState)[index].answer) {
      rightAnswerCount = rightAnswerCount + 1;
    }
  }

  results.number = rightAnswerCount;
  results.percentage = (rightAnswerCount / unref(questionsState).length) * 100;
}

onMounted(() => {
  unref(questionsState).forEach(() => answers.value.push(''));
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
