<script setup>
import {ref, computed} from 'vue'

const questions = ref([
  {
    question: 'Which is the best way to improve user engagement?',
    answer: 0,
    options: [
      'A quiz',
      'A boxing match',
      'self-flagellation'
    ],
    selected: null
  },
  {
    question: 'How much fun is it working at Tui Media?',
    answer: 2,
    options: [
      'Not bad',
      'Rather good',
      'Off the charts!'
    ],
    selected: null
  },
  {
    question: 'Give Giles the Dev job?',
    answer: 1,
    options: [
      'You’re-aving-a-larf!',
      'Certainly',
      'Keep him guessing for a couple of weeks :D'
    ],
    selected: null
  },
  {
    question: 'Enjoying this quiz?',
    answer: 0,
    options: [
      'Hells yeah!',
      'I’ve seen better',
      'Not really'
    ],
    selected: null
  },
  {
    question: 'Is this the last question?',
    answer: 1,
    options: [
      'I doubt it',
      'I think so',
      'Aren’t there usually ten?'
    ],
    selected: null
  },
])

const quizCompleted = ref(false)
const currentQuestion = ref(0)
const score = computed(() => {
  let value = 0
  // compare option selected to answer value
  questions.value.map(q => {
    if(q.selected == q.answer){
      value++
    }
  })
  return value
})

const getCurrentQuestion = computed(() => {
    let question = questions.value[currentQuestion.value]
    question.index = currentQuestion.value
    return question
})

const SetAnswer = ev => {
  // assign Q selected property to value of clicked elem
  questions.value[currentQuestion.value].selected = ev.target.value
  // reassign to null ready for next question
  ev.target.value = null
}

const NextQuestion = () => {
    if(currentQuestion.value < questions.value.length - 1) {
      currentQuestion.value++
    } else {
      quizCompleted.value = true
    }
}

const restart = () => window.location.reload()

</script>

<template>

<main class="app">
  <h1>Giles' Vue3 Quiz</h1>

  <section class="quiz" v-if="!quizCompleted">
    <div class="quiz-info">
      <span class="question">{{ getCurrentQuestion.question }}</span>
      <span class="score">Score {{ score }} / {{ questions.length }}</span>
    </div>

      <div class="options">
        <label v-for="(option, index) in getCurrentQuestion.options" 
        :key="index"
        :class="`option ${ // possible improvement - move this logic to a method 
          // if this option is selected and the index matches the answer  then add class 'correct', else add class 'incorrect', else add nowt.
          getCurrentQuestion.selected == index
          ? index == getCurrentQuestion.answer
            ? 'correct'
            : 'incorrect'
          : ''
        } ${
          // if an option other than this one is selected then add class 'disabled', else add nowt.
          getCurrentQuestion.selected != null &&
          index != getCurrentQuestion.selected
            ? 'disabled'
            : ''
        }`">
          <input
              type="radio" 
              :name="getCurrentQuestion.index"
              :value="index"
              v-model="getCurrentQuestion.selected" 
              :disabled="getCurrentQuestion.selected"
              @change="SetAnswer">
          <!-- 
            Model input's value with current question's selected value. 
            Add disabled attr to all input elems if one is selected.
            OnChange pass event to setAnswer.
          -->
          <span>{{ option }}</span>

        </label>
    </div>

    <button 
        class="centered"
        @click="NextQuestion"
        :disabled="!getCurrentQuestion.selected"
        >
        {{ 
          getCurrentQuestion.index == questions.length - 1
              ? 'finish'
              : getCurrentQuestion.selected == null
                ? 'Select an answer'
                : 'Next Question'
        }}
    </button>
  </section>

  <section v-else>
    <div class="results">
      <h2 class="shake">Nice one!<br>You have finished Giles' Vue3 quiz.</h2>
      <div class="flex">
        <div class="score">
          <p>You scored {{ score }} / {{ questions.length }}</p>
        </div>
        <button class="restart" @click="restart">Restart</button>
    </div>
    </div>
  </section>
</main>

</template>

<style lang="scss">

@import './assets/styles.scss';

</style>
