<template>
  <template v-if="this.answers">
    <div>
      <h1 v-html="this.question"></h1>
    </div>

    <template v-for="answer in this.answers" :key="answer">
      <input type="radio" name="option" id="option" :value="answer" v-model="this.chosenAnswer">
      <label for="option" v-html="answer"></label><br>
    </template>
    <button @click="this.submitAnswer" type="button" class="send">Enviar</button>
  </template>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      endpoint: "https://opentdb.com/api.php?amount=10&category=18",
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined
    }
  },
  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);

      return answers;
    }
  },
  methods: {
    submitAnswer() {
      if (this.chosenAnswer == undefined) {
        alert("Pick on of the options");
      } else {
        if (this.chosenAnswer == this.correctAnswer) {
          alert("You got it right!");
        } else {
          alert("You got it wrong!");
        }
      }
    }
  },
  created() {
    this.axios
      .get(this.endpoint)
      .then(response => {
        this.question = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
      })
  }
}
</script>

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
