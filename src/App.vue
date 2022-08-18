<template>
  <div id="guess-number align-items-center d-flex justify-content-center p-3" class="p-3">
    <div id="quiz" v-if="!finish">
      <label for="stepRange" class="form-label">Current Step: {{ steps }}</label>
      <input class="form-range" id="stepRange" type="range" v-model="steps" min="3" max="7">
      <div>
        <p>Mind a number between 1 and {{ 2 ** steps }}, then check is your number between numbers below {{ steps }}
          times, after that I'll tell your number!</p>
      </div>
      <div id="numbers">
        <ul class="d-flex flex-wrap p-0 m-0">
          <li v-for="number in previewNumbers" :key="number">{{ number }}</li>
        </ul>
      </div>
      <div class="input-group justify-content-center align-items-center">
        <button class="btn btn-outline-success form-control" type="button" @click="setPositive">Yes</button>
        <button class="btn btn-outline-danger form-control" type="button" @click="setNegative">No</button>
      </div>
    </div>
    <div id="result" class="text-center" v-else>
      <p>Your number is {{ numbers[0] }}!</p>
      <button type="button" class="btn btn-info" @click="start">Restart</button>
    </div>
  </div>

</template>

<script>
export default {
  name: "App",
  data() {
    return {
      steps: 6,
      numbers: [],
      wrongNumbers: [],
      finish: false,
    }
  },
  mounted() {
    this.start()
  },
  watch: {
    steps() {
      this.start()
    },
    numbers(value) {
      if (value.length === 1) {
        this.finish = true
      }
    },
  },
  computed: {
    firstHalf() {
      let numbers = this.numbers.slice()
      return numbers.splice(0, this.middle)
    },
    lastHalf() {
      let numbers = this.numbers.slice()
      return numbers.splice(-this.middle)
    },
    middle() {
      return Math.ceil(this.numbersCount / 2)
    },
    numbersCount() {
      return this.numbers.length
    },
    previewNumbers() {
      return this.lastHalf.slice().concat(this.wrongNumbers.slice(2 ** this.steps / 2 - this.lastHalf.length)).sort((a, b) => a - b)
    },
  },
  methods: {
    start() {
      this.finish = false
      this.wrongNumbers = []
      // noinspection JSVoidFunctionReturnValueUsed
      this.numbers = this.shuffle(this.range(2 ** this.steps, 1))
    },
    /**
     * @copyright https://stackoverflow.com/a/10050831/19113328
     * @static
     * @param {Number} size - Length of range
     * @param {Number} startAt  - Starting number of range
     * @returns {ReadonlyArray<Number>} - Readonly array of numbers
     */
    range(size, startAt = 0) {
      return [...Array(size).keys()].map(i => i + startAt)
    },

    /**
     * @copyright https://stackoverflow.com/a/2450976/19113328
     * @static
     * @param {Array} array
     * @returns {Array}
     */
    shuffle(array) {
      let currentIndex = array.length, randomIndex;

      // While there remain elements to shuffle.
      while (currentIndex !== 0) {

        // Pick a remaining element.
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex--;

        // And swap it with the current element.
        [array[currentIndex], array[randomIndex]] = [
          array[randomIndex], array[currentIndex]];
      }

      return array;
    },
    setNegative() {
      this.wrongNumbers = this.wrongNumbers.concat(this.lastHalf)
      this.numbers = this.firstHalf
    },
    setPositive() {
      this.wrongNumbers = this.wrongNumbers.concat(this.firstHalf)
      this.numbers = this.lastHalf
    },
  }
}
</script>

<style>
html, body {
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

body {
  background: url(@/img/bg.png);
}

/*noinspection ALL*/
#app {
  border: 1px solid rgb(255 255 255 / 30%);
  border-radius: 10px;
  box-shadow: 0 0 5px 0 #00000030;
  backdrop-filter: blur(20px) brightness(1.1);
}

#app * {
  user-select: none;
}

#guess-number {
  min-height: 200px;
}

#numbers {
  padding: 10px;
  background: #ffffff3b;
  border: 1px solid #ffffff42;
  border-radius: 10px;
  margin: 20px 0;
  box-shadow: 0 0 4px -2px;
}

#numbers li {
  width: 40px;
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
