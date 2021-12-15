<template>
<div class="min-h-full mt-6">
  <!--<Questions/>-->
  <h1 class="text-2xl font-semibold">{{titleState}}</h1>
  <div v-if="questionsState">
    <form @submit.prevent="handleSubmit">
      <div class="shadow overflow-hidden sm:rounded-md">
        <div class="px-4 py-5 bg-white space-y-6 sm:p-6">
          <fieldset
            v-for="(question, index) in questionsState"
            :key="index" :id="'Q'+index"
            class="relative px-6 pt-10 pb-8 bg-white shadow-xl ring-2 xs:max-w-lg md:max-w-full sm:mx-auto rounded-lg sm:px-10"
            :class="{'ring-gray-900/5' : answers[index] !== '', 'ring-yellow-400' : index === unanswered[index]}"
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
        <div class="px-4 py-3 text-center sm:px-6 text-base leading-7 font-semibold">
          <button type="submit" class="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
            Submit
          </button>
        </div>
      </div>
    </form>
  </div>
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
  return unref(questionsState).map((question: any) => {
    return question.choices;
  })
})

const handleSubmit = () => {
  //?: No need to do any array validation as the answers array is dynamically filled to the length of the state
  //TODO: If any of the answers in the answers array is blank, add error class to panel relating to index
  //TODO: If everything is good, call grading function
  unanswered.value = [];
  const errorCheck = [] as any;

  for (let index = 0; index < unref(answers).length; index++) {
    const element = unref(answers)[index];
    if (element === '') {
      errorCheck.push(index);
      unref(unanswered).push(index);
    }
  }

  if (errorCheck.length != 0) {
    errorCheck.forEach(item => {
      const itemCheck = document.getElementById(`Q${item}`);
      if (itemCheck) {

        //TODO: These contradict the conditional template classes when removing class so have to check again when cleaning up
        //itemCheck.classList.remove('ring-gray-900/5');
        itemCheck.classList.add('ring-yellow-400');
      }
      console.log(itemCheck);
    });
    console.log(errorCheck)
    //TODO: Return error and change ring color yellow for each item in errorCheck
    console.log('Answer all questions before submitting. Unanswered questions are displayed in yellow.')
  } else {
    console.log('sent');
    grading();
  }
}

const grading = () => {
  //TODO: Need to check each answer in answers array against questionState.answer based on index
  //TODO: If values do not match, add error class and messages to panel
  //TODO: Highlight correct answer based on index of question
  //TODO: If correct, add success class and message to panel
  let rightAnswerCount = 0;

  for (let index = 0; index < unref(answers).length; index++) {
    const element = unref(answers)[index];
    if (element === unref(questionsState)[index].answer) {
      rightAnswerCount = rightAnswerCount + 1;
    }
  }

  console.log(`you got ${rightAnswerCount} out of ${unref(questionsState).length} questions correct`)
  console.log('percentage: ', (rightAnswerCount/unref(questionsState).length) * 100, '%');
}


onMounted(() => {
  questionsState.value = Q
  //titleState.value = title;
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
