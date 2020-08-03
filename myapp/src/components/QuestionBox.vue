<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>{{ currentQuestion.question }}</template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item v-for="(answer, index) in shuffleListAnswers" :key="index"
        @click="selectAnswer(index)"
        :class="answerClass(index)">{{ answer }}</b-list-group-item>
      </b-list-group>

      <b-button 
        variant="primary" 
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
      >
        Submit
      </b-button>
      <b-button @click="next" variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  name: "QuestionBox",
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      shuffleListAnswers: [],
      correctIndex: null,
      answered: false
    }
  },
  computed: {
    answers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      return answers;
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      }
    }
    
    // () {
    //   this.selectedIndex = null;
    //   this.shuffleAnswers();
    // }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      this.shuffleListAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffleListAnswers.indexOf(this.currentQuestion.correct_answer);
    },
    submitAnswer() {
      let isCorrect = false;

      if(this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }

      this.answered = true;

      this.increment(isCorrect);
    },

    answerClass(index) {
      let answerClass = '';

      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected';
      } else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
        answerClass = 'incorrect';
      }

      return answerClass;
    }
  },
};
</script>

<style scoped>
  .list-group {
    margin-bottom: 15px;
  }

  .list-group-item:hover {
    background: #eee;
    cursor: pointer;
  }

  .btn {
    margin: 0 5px;
  }

  .selected {
    background: lightblue;
  }

  .correct {
    background: green;
  }

  .incorrect {
    background: red;
  }
</style>