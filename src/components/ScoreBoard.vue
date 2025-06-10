<template>

    <section class="score">
      Jogador <span>{{ this.winCount }}</span> x 
     <span>{{ this.loseCount }}</span> Computador
     <button class="btn-reset" @click="resetScore">Zerar placar</button>
    </section>

</template>

<script>

    export default {
        props: ['winCount', 'loseCount'],
        methods: {
            resetScore(){
                this.$emit('reset')
            }
        }
    }

</script>


<style lang="scss">

section.score {
  border-bottom: 1px solid black;
  padding: 24px;
  font-size: 18px;
  position: relative;
  span {
    padding: 8px;
    font-weight: bold;
    border: 1px solid black;
  }
}

.btn-reset{
    border-radius: 6px;
    background: #ad2424;
    color: #fff;
    border: none;
    padding: 8px 16px;
    cursor: pointer;
    margin-top: 24px;
}
@media (min-width: 768px) {
    .btn-reset{
        position: absolute;
        margin: 0;
        right: 0;
    }
}
</style>