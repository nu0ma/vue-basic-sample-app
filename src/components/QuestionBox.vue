<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>{{ currentQuestion.question }}</template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(ansewr, index) in shuffledAnswers"
          :key="index"
          @click="selectAnswer(index)"
          :class="[selectedIndex === index ? 'selected' : '']"
        >{{ ansewr }}</b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null  || answerd"
      >Submit</b-button>
      <b-button @click="next" variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      correctIndex: null,
      answerd: false
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    }
  },
  // watchはpropsと同じ名前のメソッドをかくと、そのpropsを受け取った時にpropsに追加の処理を行うことができる
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.shuffleAnswers();
        this.answerd = false;
      }
    }
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
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
      console.log(this.currentQuestion.correct_answer);
    },
    submitAnswer() {
      let isCorrect = false;

      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answerd = true;

      this.increment(isCorrect);
    }
  }
  // mounted() {
  //   this.shuffleAnswers();
  //   console.log(this.currentQuestion);
  // },
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}

.btn {
  margin: 0 5px;
}

.list-group-item:hover {
  background: #eee;
  cursor: pointer;
}

.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: red;
}
</style>
