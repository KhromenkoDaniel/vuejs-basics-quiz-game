<script>
import ScoreBoard from '@/components/ScoreBoard.vue'

export default {
  name: 'App',
  components: { ScoreBoard },
  data() {
    return {
      chosen_answer: undefined,
      question: undefined,
      incorrectAnswers: [],
      correctAnswer: '',
      win_count: 0,
      lose_count: 0,
      answerSubmitted: false
    }
  },
  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers))
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer)
      return answers
    }
  },
  methods: {
    submitAnswer() {
      if (!this.chosen_answer) {
        alert('Pick one of the options')
      } else {
        this.answerSubmitted = true
        if (this.chosen_answer === this.correctAnswer) {
          this.win_count++
          localStorage.setItem('win_count', this.win_count)
        } else {
          this.lose_count++
          localStorage.setItem('lose_count', this.lose_count)
        }
      }
    },
    getNewQuestion() {
      this.chosen_answer = undefined
      this.answerSubmitted = false
      this.question = undefined

      this.axios
        .get('https://opentdb.com/api.php?amount=1&category=30')
        .then(
          (response) => (
            (this.question = response.data.results[0].question),
            (this.incorrectAnswers = response.data.results[0].incorrect_answers),
            (this.correctAnswer = response.data.results[0].correct_answer)
          )
        )
    },
    resetScore() {
      this.win_count = 0
      this.lose_count = 0
      localStorage.removeItem('win_count')
      localStorage.removeItem('lose_count')
    }
  },
  created() {
    this.getNewQuestion()
    switch (true) {
      case !localStorage.getItem('win_count') && !localStorage.getItem('lose_count'):
        this.win_count = 0
        this.lose_count = 0
        break
      case !localStorage.getItem('win_count'):
        this.win_count = 0
        this.lose_count = localStorage.getItem('lose_count')
        break
      case !localStorage.getItem('lose_count'):
        this.win_count = localStorage.getItem('win_count')
        this.lose_count = 0
        break
      default:
        this.win_count = localStorage.getItem('win_count')
        this.lose_count = localStorage.getItem('lose_count')
    }
  }
}
</script>
<template>
  <ScoreBoard :win_count="win_count" :lose_count="lose_count" :resetScore="resetScore" />

  <template v-if="this.question">
    <h1 v-html="this.question"></h1>

    <template v-for="(answer, index) in this.answers" v-bind:key="index">
      <input
        :disabled="this.answerSubmitted"
        type="radio"
        name="options"
        :value="answer"
        v-model="this.chosen_answer"
      />

      <label v-html="answer"></label><br />
    </template>

    <button v-if="!this.answerSubmitted" @click="this.submitAnswer()" class="send" type="button">
      Send
    </button>
  </template>

  <section class="result" v-if="this.answerSubmitted">
    <template v-if="this.chosen_answer === this.correctAnswer">
      <h4>&#9989; Congratulations, the answer "{{ this.correctAnswer }}" is correct.</h4>
    </template>
    <template v-else>
      <h4>
        &#10060; I'm sorry, you picked the wrong answer. The correct is "{{ this.correctAnswer }}".
      </h4>
    </template>
    <button @click="this.getNewQuestion()" class="send" type="button">Next question</button>
  </section>
</template>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;
}

h1 {
  margin-top: 40px;
}

input[type='radio'] {
  margin: 12px 4px;
}

button.send {
  margin-top: 12px;
  height: 40px;
  min-width: 120px;
  padding: 0 16px;
  color: #fff;
  background-color: #1867c0;
  border: 1px solid #1867c0;
  cursor: pointer;
  border-radius: 14px;
}

section.score {
  border-bottom: 1px solid black;
  padding: 24px;
  font-size: 18px;

  span {
    padding: 8px;
    font-weight: bold;
    border: 1px solid black;
  }
}
</style>
