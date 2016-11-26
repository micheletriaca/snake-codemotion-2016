<template>
  <div style="text-align: center">
    <transition name="woow" @after-enter="easterEgg = false">
      <img class="achievement" src="/static/img/l_black.png" v-if="easterEgg" />
    </transition>

    <h1>Snake (invio per iniziare)</h1>
    <div
      v-focus
      tabindex="0"
      focus="true"
      id="snake-container"
      @keydown.37="direction = 'left'"
      @keydown.38="direction = 'top'"
      @keydown.39="direction = 'right'"
      @keydown.40="direction = 'bottom'"
      @keyup.enter="startGame"
    >
      <template v-for="s in snakeChunks">
        <snake-chunk :position="s" :chunk-size="chunkSize"></snake-chunk>
      </template>
      <ball :position="ballPosition"></ball>
    </div>
    <h1 class="game-over" v-if="gameover">GAME OVER</h1>
  </div>
</template>

<script>
import SnakeChunk from './components/SnakeChunk.vue'
import Ball from './components/Ball.vue'
import _ from 'lodash'
import Vue from 'vue'

Vue.directive('focus', {
  inserted: function (el) {
    el.focus()
  }
})

export default {
  name: 'app',
  components: {
    SnakeChunk,
    Ball
  },
  data () {
    return {
      snakeChunks: [],
      direction: 'right',
      prevDirection: 'right',
      ballPosition: {},
      clock: undefined,
      chunkSize: 20,
      gameover: false,
      easterEgg: false
    }
  },
  computed: {
    numStepsX () {
      return 400 / this.chunkSize
    },
    numStepsY () {
      return 300 / this.chunkSize
    }
  },
  methods: {
    placeBall () {
      this.ballPosition = {
        x: Math.floor(Math.random() * (this.numStepsX - 1)) + 1,
        y: Math.floor(Math.random() * (this.numStepsY - 1)) + 1
      }
    },
    endGame () {
      clearInterval(this.clock)
      this.gameover = true
    },
    startGame () {
      this.gameover = false
      this.direction = 'right'
      this.prevDirection = 'right'

      this.snakeChunks = [
        {left: 0, top: 0},
        {left: 1, top: 0},
        {left: 2, top: 0}
      ]

      this.ball = this.placeBall()

      clearInterval(this.clock)

      this.clock = setInterval(() => {
        let head = _.last(this.snakeChunks)

        /* Prevengo click a sx se mi muovo verso destra e similari */
        if (
          this.prevDirection === 'left' && this.direction === 'right' ||
          this.prevDirection === 'top' && this.direction === 'bottom' ||
          this.prevDirection === 'bottom' && this.direction === 'top' ||
          this.prevDirection === 'right' && this.direction === 'left'
        ) this.direction = this.prevDirection

        /* Se esco dalla cornice sono morto */
        if (
          head.left === 0 && this.direction === 'left' ||
          head.left === this.numStepsX - 1 && this.direction === 'right' ||
          head.top === 0 && this.direction === 'top' ||
          head.top === this.numStepsY - 1 && this.direction === 'bottom'
        ) return this.endGame()

        /* Se ho mangiato una pallina, non elimino la coda e riposiziono la pallina */
        if (head.left === this.ballPosition.x && head.top === this.ballPosition.y) {
          this.placeBall()
        } else {
          /* Elimino ultimo elemento dalla coda. */
          this.snakeChunks.shift()
        }

        /* Creo la nuova testa e la posiziono */
        head = Object.assign({}, head)
        switch (this.direction) {
          case 'right':
            head.left++
            break
          case 'top':
            head.top--
            break
          case 'bottom':
            head.top++
            break
          case 'left':
            head.left--
            break
        }

        /* Check collisioni */
        let collision = _.find(this.snakeChunks, {left: head.left, top: head.top})
        if (collision) return this.endGame()

        /* Aggiungo la testa */
        this.snakeChunks.push(head)

        this.prevDirection = this.direction
      }, 100)
    }
  }
}
</script>
<style>
  #snake-container {
    position: relative;
    width: 400px;
    height: 300px;
    border: 1px solid black;
    overflow: hidden;
    display: inline-block;
  }

  .game-over {
    color: red;
  }

  .achievement {
    position: fixed;
    left: 50%;
    top: 50%;
    width: 10px;
    height: 10px;
    display: none;
  }

  .woow-enter-active {
    display: block;
    transform-origin: center center;
    animation: bounce-in 1s;
  }
  .woow-leave-active {
    display: none;
  }

  @keyframes bounce-in {
    0% {
      transform: scale(0, 0);
      opacity: 1;
    }
    100% {
      transform: scale(200, 200);
      opacity: 0;
    }
  }
</style>
