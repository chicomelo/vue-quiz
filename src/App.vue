
<template>

  <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount" @reset="resetScore"/>

  <template v-if="question && answers.length">
    <h1 v-html="question"></h1>
    
    <template v-for="(answer, index) in answers" :key="index">
      <label>
        <input 
        :disabled="this.answerSubmitted"
        type="radio" 
        name="options" 
        :value="answer"
        v-model="this.chosenAnswer"
        /> 
        <span v-html="answer"></span>
      </label><br />
    </template>
    <button class="send" type="button" @click="this.submitAnswer()"
    v-if="!this.answerSubmitted">Enviar</button>

    <section class="result" v-if="this.answerSubmitted">
      <template v-if="this.chosenAnswer == this.correctAnswer">
        <h4 v-html="'&#9989; Parabéns, a resposta ' + this.correctAnswer + ' está correta.'"></h4>
      </template>
      <template v-else>
        <h4 v-html="'&#10060;  Que pena, a resposta está errada. A resposta correta é ' + this.correctAnswer + '.'"></h4>
      </template>
      <button @click="this.getNewQuestion()" class="send" type="button">Próxima pergunta</button>
    </section>
    
  </template>
  <template v-else>
    <h2>🔄 Carregando pergunta...</h2>
  </template>
</template>

<script>

import ScoreBoard from '@/components/ScoreBoard.vue'

export default {
  name: 'App',
  components: {
    ScoreBoard
  },
  data(){
    return {
      scoreStorage: [],
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0
    }
  },
  methods: {
    submitAnswer(){
      if(!this.chosenAnswer){
        alert('Pick one of the options')
      }else{
        this.answerSubmitted = true
        if(this.chosenAnswer === this.correctAnswer){
          this.winCount++
        }else{
          this.loseCount++
        }
      }
      this.salvarPlacar()
    },
    salvarPlacar() {
      localStorage.setItem('@score', JSON.stringify({
        win: this.winCount,
        lose: this.loseCount
      }))
    },
    carregarPlacar() {
      const score = localStorage.getItem('@score')
      if (score) {
        const parsed = JSON.parse(score)
        this.winCount = parsed.win || 0
        this.loseCount = parsed.lose || 0
      }
    },
    getNewQuestion(){

      this.answerSubmitted = false
      this.chosenAnswer = undefined
      this.question = undefined
      this.incorrectAnswers = undefined
      this.correctAnswer = undefined

      this.axios
      .get('https://opentdb.com/api.php?amount=1&category=18&type=multiple')
      .then((response) => {
        this.question = response.data.results[0].question
        this.incorrectAnswers = response.data.results[0].incorrect_answers
        this.correctAnswer = response.data.results[0].correct_answer
      })
    },
    resetScore() {
      this.winCount = 0
      this.loseCount = 0
      localStorage.removeItem('@score')
    }
  },
  computed: {
    answers() {
      if (!this.incorrectAnswers || !this.correctAnswer) {
        return []
      }
      const answers = [...this.incorrectAnswers]
      answers.splice(Math.floor(Math.random() * (answers.length + 1)), 0, this.correctAnswer)
      return answers
    }
  },
  created(){
    this.getNewQuestion()
    this.carregarPlacar()
  }

}

//https://opentdb.com/api.php?amount=1&category=18


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
 
  input[type='radio']{
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
}
 
 
</style>
