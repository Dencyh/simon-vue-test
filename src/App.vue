<template>
  <h1>Simon The Game</h1>
  <div class="wrapper">
    <div class="button-container">
      <button class="btn" @click="startGame">Start</button>
      <div class="row">
        <div
          id="1"
          class="plate blue"
          @mousedown="press"
          @mouseup="release"
          @mouseout="release"
        ></div>
        <div
          id="2"
          class="plate red"
          @mousedown="press"
          @mouseup="release"
          @mouseout="release"
        ></div>
      </div>
      <div class="row">
        <div
          id="3"
          class="plate yellow"
          @mousedown="press"
          @mouseup="release"
          @mouseout="release"
        ></div>
        <div
          id="4"
          class="plate green"
          @mousedown="press"
          @mouseup="release"
          @mouseout="release"
        ></div>
      </div>
    </div>

    <aside class="controls">
      <label class="label">Difficulty</label>
      <select class="input" v-model="setDifficulty">
        <option class="option" value="easy">Easy</option>
        <option class="option" value="normal">Normal</option>
        <option class="option" value="hard">Hard</option>
      </select>
      <h2>Round: {{ round }}</h2>
      <aside v-if="lost">
        <h2 class="lose">You lost</h2>
        <h3>Click start to play again</h3>
      </aside>
    </aside>
  </div>
</template>

<script>
const soundBlue = require("./assets/f1.mp3");
const soundRed = require("./assets/g2.mp3");
const soundYellow = require("./assets/h3.mp3");
const soundGreen = require("./assets/k4.mp3");
export default {
  data() {
    return {
      audio: {
        1: soundBlue,
        2: soundRed,
        3: soundYellow,
        4: soundGreen,
      },
      soundToPlay: null,
      button: {
        pressed: false,
      },
      setDifficulty: "normal",
      difficulty: {
        easy: 1500,
        normal: 1000,
        hard: 400,
      },
      order: [],
      orderHistory: [],
      round: 0,
      gameOn: false,
      boardActive: false,
      lost: false,
    };
  },
  methods: {
    press(e) {
      if (!this.boardActive) return;
      if (this.button.pressed) return;
      this.button.pressed = true;

      /* stop previous sound */
      /* if(this.soundToPlay) this.soundToPlay.pause() */
      e.target.classList.add("active");

      /* play sound */
      this.soundToPlay = new Audio(this.audio[e.target.id]);
      this.soundToPlay.play();

      /* check input */
      this.registerClick(e);
    },
    release(e) {
      /*    if (this.boardActive) return */
      /* min time enabled */
      if (this.button.pressed) {
        setTimeout(() => {
          this.button.pressed = false;
          e.target.classList.remove("active");
        }, 50);
      }
    },
    registerClick(e) {
      const validInput = this.orderHistory.shift();
      const userInput = e.target.id;
      this.gameOn = parseInt(validInput, 10) === parseInt(userInput, 10);

      this.checkLose();
    },
    startGame() {
      this.lost = false;
      this.order = [];
      this.orderHistory = [];
      this.round = 1;
      this.gameOn = true;

      this.boardActive = true;
      this.newRound();
    },
    newRound() {
      this.order.push(this.generateNumber());
      this.orderHistory = this.order.slice(0);
      this.animate(this.order);
    },

    checkLose() {
      if (this.orderHistory.length === 0 && this.gameOn) {
        /* this.deactivateBoard(); */

        setTimeout(() => {
          this.newRound();
          this.round++;
        }, 300);
      } else if (!this.gameOn) {
        this.boardActive = false;
        this.round = 0;
        this.lost = true;
      }
    },
    changeDifficulty(e) {
      this.setDifficulty = this.difficulty[e.target.value];
    },

    animate() {
      this.boardActive = false;
      let i = 0;

      let interval = setInterval(() => {
        this.playSound(this.order[i]);
        this.flashPlate(this.order[i]);

        i++;

        if (i >= this.order.length) {
          clearInterval(interval);
          this.boardActive = true;
        }
      }, [this.difficulty[this.setDifficulty]]);
    },
    playSound(soundId) {
      this.soundToPlay = new Audio(this.audio[soundId]);
      this.soundToPlay.play();
    },
    flashPlate(id) {
      const target = document.getElementById(`${id}`);
      target.classList.add("active");
      setTimeout(() => {
        target.classList.remove("active");
      }, [this.difficulty[this.setDifficulty]] - 200);
    },
    generateNumber() {
      return Math.floor(Math.random() * 4 + 1);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}

.wrapper {
  margin-top: 50px;
  display: flex;
  justify-content: center;
}

.button-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
}

.row {
  display: flex;
}

.plate {
  width: 50px;
  height: 50px;
  cursor: pointer;
  opacity: 0.6;
  transform: translate3d(0, 0, 0);
  transition: transform 150ms ease;
}

.plate.blue {
  background: none;
  border-left: 150px solid rgb(65, 65, 255);
  border-top: 150px solid rgb(65, 65, 255);
  border-top-left-radius: 100%;
}
.plate.blue:hover {
  -webkit-transform: translateX(-3px) translateY(-3px) scale(103%);
  -moz-transform: translateX(-3px) translateY(-3px) scale(103%);
  -o-transform: translateX(-3px) translateY(-3px) scale(103%);
  -ms-transform: translateX(-3px) translateY(-3px) scale(103%);
  transform: translateX(-3px) translateY(-3px) scale(103%);
}
.plate.red {
  background: none;
  border-right: 150px solid #ff5643;
  border-top: 150px solid #ff5643;
  border-top-right-radius: 100%;
}
.plate.red:hover {
  transform: translate3d(0);
  -webkit-transform: translateX(3px) translateY(-3px) scale(103%);
  -moz-transform: translateX(3px) translateY(-3px) scale(103%);
  -o-transform: translateX(3px) translateY(-3px) scale(103%);
  -ms-transform: translateX(3px) translateY(-3px) scale(103%);
  transform: translateX(3px) translateY(-3px) scale(103%);
}

.plate.yellow {
  background: none;
  border-left: 150px solid rgba(255, 255, 0);
  border-bottom: 150px solid rgb(255, 255, 0);
  border-bottom-left-radius: 100%;
}
.plate.yellow:hover {
  -webkit-transform: translateX(-3px) translateY(3px) scale(103%);
  -moz-transform: translateX(-3px) translateY(3px) scale(103%);
  -o-transform: translateX(-3px) translateY(3px) scale(103%);
  -ms-transform: translateX(-3px) translateY(3px) scale(103%);
  transform: translateX(-3px) translateY(3px) scale(103%);
}
.plate.green {
  background: none;
  border-right: 150px solid rgb(78, 255, 78);
  border-bottom: 150px solid rgb(78, 255, 78);
  border-bottom-right-radius: 100%;
}
.plate.green:hover {
  -webkit-transform: translateX(3px) translateY(3px) scale(103%);
  -moz-transform: translateX(3px) translateY(3px) scale(103%);
  -o-transform: translateX(3px) translateY(3px) scale(103%);
  -ms-transform: translateX(3px) translateY(3px) scale(103%);
  transform: translateX(3px) translateY(3px) scale(103%);
}

.plate.blue.active {
  opacity: 1;
}
.plate.red.active {
  opacity: 1;
}
.plate.yellow.active {
  opacity: 1;
}
.plate.green.active {
  opacity: 1;
}

.controls {
  width: 300px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.btn {
  width: 110px;
  height: 110px;
  z-index: 1;
  top: 50%;
  transform: translateY(-50%);
  border: 1px solid black;
  border: 1px solid black;
  border-radius: 50%;
  cursor: pointer;
  user-select: none;
  color: white;
  display: block;
  font-size: 20px;
  font-weight: 700;
  text-transform: uppercase;
  background: rgb(51, 61, 77);
  position: absolute;
}

select {
  cursor: pointer;
}
.input {
  height: 35px;
  padding: 5px 6px;
  border: none;
  border: 2px solid rgb(56, 68, 94);
  color: black;
  outline: none;
  font-size: 16px;
  font-weight: 700;
}
.label {
  color: black;
  display: inline-block;
  font-size: 16px;
  text-transform: uppercase;
  font-weight: 700;
  letter-spacing: 0.7px;
}

.lose {
  text-transform: uppercase;
  color: rgb(238, 49, 49);
}
</style>
