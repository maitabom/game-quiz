<template>
  <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount" />

  <template v-if="this.answers">
    <div>
      <h1 v-html="this.question"></h1>
    </div>

    <template v-for="answer in this.answers" :key="answer">
      <input type="radio" name="option" id="option" :value="answer" :disabled="this.answerSubmitted"
        v-model="this.chosenAnswer">
      <label for="option" v-html="answer"></label><br>
    </template>
    <button v-if="!this.answerSubmitted" @click="this.submitAnswer" type="button" class="send">Enviar</button>

    <section class="result" v-if="this.answerSubmitted">
      <template v-if="this.chosenAnswer == this.correctAnswer">
        <h4>&#9989; Parabéns, a resposta "{{ this.correctAnswer }}" está correta.</h4>
      </template>
      <template v-else>
        <h4>&#10060; Que pena, a resposta está errada. A resposta correta é "{{ this.correctAnswer }}".</h4>
      </template>
      <button  @click="this.getNewQuestion()" class="send" type="button">Próxima pergunta</button>
    </section>
  </template>
</template>

<script>
import ScoreBoard from './components/ScoreBoard.vue';

export default {
  name: 'App',
  components: {
    ScoreBoard
  },
  data() {
    return {
      endpoint: "https://opentdb.com/api.php?amount=10&category=18",
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      winCount: 0,
      loseCount: 0,
      answerSubmitted: false
    }
  },
  computed: {
    answers() {
      var options = this.incorrectAnswers ? JSON.parse(JSON.stringify(this.incorrectAnswers)) : new Array();
      options.splice(Math.round(Math.random() * options.length), 0, this.correctAnswer);

      return options;
    }
  },
  methods: {
    submitAnswer() {
      if (!this.chosenAnswer) {
        alert('Pick one of the options');
      } else {
        this.answerSubmitted = true;
        if (this.chosenAnswer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },
    getNewQuestion() {
      this.chosenAnswer = undefined;
      this.answerSubmitted = false;
      this.question = undefined;

      this.axios
        .get(this.endpoint)
        .then(response => (
          this.question = response.data.results[0].question,
          this.incorrectAnswers = response.data.results[0].incorrect_answers,
          this.correctAnswer = response.data.results[0].correct_answer
        ))
    }
  },
  created() {
    this.getNewQuestion();
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


</style>
