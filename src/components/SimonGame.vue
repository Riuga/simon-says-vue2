<template>
  <div class="game">
    <h1>Simon Says</h1>
    <div class="playground">
      <div class="playground-btns blue" @click="registerclick" title="0" ref="highlightBlue"></div>
      <div class="playground-btns red" @click="registerclick" title="1" ref="highlightRed"></div>
      <div class="playground-btns yellow" @click="registerclick" title="2" ref="highlightYellow"></div>
      <div class="playground-btns green" @click="registerclick" title="3" ref="highlightGreen"></div>
    </div>
    <div class="start">
      <h2>Round: {{ rounds }}</h2>
      <p v-if="lose">You lost after {{ rounds }} rounds</p>
      <button @click="startGame">Start game</button>
    </div>
    <div class="settings" v-if="!isGameStarted">
      <h2>Difficulty:</h2>
      <div class="difficulty">
        <input type="radio" value="0" v-model="difficulty" />
        <label>Easy</label>
        <input type="radio" value="1" v-model="difficulty" />
        <label>Medium</label>
        <input type="radio" value="2" v-model="difficulty" />
        <label>Hard</label>
      </div>
    </div>
  </div>
</template>

<script>

const audios = [
  new Audio(require('@/assets/sounds/1.mp3')),
  new Audio(require('@/assets/sounds/2.mp3')),
  new Audio(require('@/assets/sounds/3.mp3')),
  new Audio(require('@/assets/sounds/4.mp3'))]

export default {
  name: 'SimonGame',
  data() {
    return ({
      isGameStarted: false,
      lose: false,
      clickable: false,
      correct: true,
      rounds: 0,
      difficulty: 0,
      duration: 1500,
      durations: [1500, 1000, 400],
      sequence: [],
      sequenceToShift: [],
    })
  },

  methods: {
    playSound(index) {
      audios[index].play()
    },

    highlight(index) {
      const refs = [this.$refs.highlightBlue, this.$refs.highlightRed, this.$refs.highlightYellow, this.$refs.highlightGreen]
      const currentRef = refs[index]
      const oldClasses = refs[index].className
      currentRef.className = oldClasses + ' highlight'
      window.setTimeout(function () {
        currentRef.className = oldClasses
      }, 300);
    },

    startGame() {
      this.isGameStarted = true
      this.lose = false
      this.rounds = 0
      this.sequence = []
      this.sequenceToShift = []
      this.duration = this.durations[this.difficulty]
      this.newRound()
    },

    newRound() {
      ++this.rounds
      this.sequence.push(Math.floor(Math.random() * 4))
      this.sequenceToShift = this.sequence.slice(0)
      this.clickable = true
      this.animate(this.sequence)
    },

    animate(sequence) {
      let i = 0
      const that = this
      const interval = setInterval(function () {
        that.playSound(sequence[i]);
        that.highlight(sequence[i]);

        i++;
        if (i >= sequence.length) {
          clearInterval(interval);
        }
      }, that.duration);
    },

    registerclick(e) {
      if (this.clickable) {
        const requiredValue = this.sequenceToShift.shift()
        const clickedValue = e.target.title
        this.playSound(clickedValue)
        this.highlight(clickedValue)
        this.correct = (requiredValue == clickedValue)
        this.checkLose()
      }
    },

    checkLose() {
      if (this.sequenceToShift.length === 0 && this.correct) {
        this.newRound()
      }

      if (!this.correct) {
        this.endGame()
      }
    },

    endGame() {
      this.isGameStarted = false
      this.lose = true
      this.clickable = false
    }
  },
}
</script>

<style scoped>
.game {
  padding: 20px;
  display: grid;
  grid-template-areas:
    "h h h"
    "p p g"
    "p p s";
  gap: 5px;
}

@media (max-width: 1100px) {
  .game {
    grid-template-areas: "h" "g" "s" "p";
  }

  .playground {
    margin: auto 0;
  }

  .playground-btns {
    /* bottom: 0; */
    left: 4em;
  }
}

.playground {
  grid-area: p;
  margin: 0 auto;
}

.start {
  grid-area: g;
}

.settings {
  grid-area: s;
}

.difficulty {
  display: grid;
  grid-template: repeat(3, 20px) / repeat(2, 50px);
  gap: 5px;
  margin: 0 auto;
}

h1 {
  margin: 1em 0 2em;
  padding: 5px;
  grid-area: h;
  text-align: center;
}

h2 {
  padding: 5px;
}

p {
  color: red;
  padding: 5px;
}

button {
  cursor: pointer;
  width: 7em;
  box-sizing: border-box;
  font-size: 1.4em;
  border-radius: 10px;
  background: #6DABE8;
  border: none;
  padding: 0.6em;
}

.playground-btns {
  cursor: pointer;
  height: 290px;
  border-radius: 150px;
  position: absolute;
  text-indent: 10000px;
  opacity: 0.5;
}

.playground-btns:hover {
  border: 2px solid black;
}

.red {
  background: #FF5643;
  clip: rect(0px, 300px, 150px, 150px);
  width: 296px;
}

.blue {
  background: dodgerblue;
  clip: rect(0px, 150px, 150px, 0px);
  width: 300px;
}

.yellow {
  background: #FEEF33;
  clip: rect(150px, 150px, 300px, 0px);
  width: 300px;
}

.green {
  background: #BEDE15;
  clip: rect(150px, 300px, 300px, 150px);
  width: 296px;
}

.playground-btns.highlight {
  opacity: 1;
}

button:hover {
  background-color: lightskyblue;
}
</style>
