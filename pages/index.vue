<template>
  <div class="d-flex flex-column h-100">
    <header>
      <div class="navbar-dark navbar bg-dark">
        <h1 class="navbar-brand">Hangman</h1>
      </div>
    </header>

    <main class="flex-shrink-0 container mt-5">
      <!-- LOSER -->
      <div class="start-game text-center p-5" v-if="gameOver">
        <h1 class="text-danger">Game over!</h1>
        <h3>
          The word was:
          <em>{{word}}</em>
        </h3>
        <button class="btn btn-xl btn-primary" @click="startNewGame()">Start New Game</button>
      </div>

      <!-- WINNER -->
      <div class="game-won text-center p-5" v-if="gameWon">
        <h1 class="text-success">You Won!!</h1>
        <h3>
          The word was:
          <em>{{word}}</em>
        </h3>
        <button class="btn btn-xl btn-primary" @click="startNewGame()">Start New Game</button>
      </div>

      <!-- The GAME -->
      <div class="game" v-if="!gameOver && !gameWon">
        <div class="row">
          <div class="col-sm-4">
            <img :src="'/images/Hangman-'+ manStage + '.png'" />
          </div>
          <div class="col-sm-8">
            <div class="d-flex">
              <template v-for="letterSpaces in wordArray">
                <div
                  class="blank"
                  v-bind:key="letterSpaces.id"
                >{{letterSpaces.showLetter ? letterSpaces.letter : '_'}}</div>
              </template>
            </div>
          </div>
        </div>



          
          <div class="my-3 text-center">
            <p>Click a letter to play.</p>
          </div>
        <div class=" border p-3 d-flex">
          <div v-for="letter in choices" v-bind:key="letter">
            <button
              :disabled="guessed.includes(letter)"
              :title="guessed.includes(letter) ? 'Letter already guessed' : ''"
              @click="guessLetter(letter)"
              class="btn btn-outline-dark mr-2"
            >{{ letter}}</button>
          </div>
        </div>
      </div>
    </main>

    <!-- Footer for attribution of image taken from wikipedia -->
    <footer class="footer mt-auto py-3 text-center">
      <div class="container">
        <span class="text-muted">
          Images are shared by permission from
          <a
            href="https://en.wikipedia.org/wiki/Hangman_(game)"
          >Wikipedia</a> under the
          <a
            href="http://creativecommons.org/licenses/by-sa/3.0/"
            title="Creative Commons Attribution-Share Alike 3.0"
          >Creative Commons Attribution-Share Alike 3.0 License</a>
        </span>
      </div>
    </footer>
  </div>
</template>

<script>
import randomWords from "random-words";

export default {
  head: {
    htmlAttrs: {
      class: "h-100"
    },
    bodyAttrs: {
      class: "h-100"
    }
  },
  data: function() {
    return {
      gameOver: true,
      gameWon: false,
      word: "",
      manStage: 0,
      choices: [
        "a",
        "b",
        "c",
        "d",
        "e",
        "f",
        "g",
        "h",
        "i",
        "j",
        "k",
        "l",
        "m",
        "n",
        "o",
        "p",
        "q",
        "r",
        "s",
        "t",
        "u",
        "v",
        "w",
        "X",
        "y",
        "z"
      ],
      guessed: [],
      wordArray: []
    };
  },
  methods: {
    startNewGame() {
      this.gameWon = false;
      this.manStage = 0;
      this.gameOver = false;
      this.guessed = [];

      // scaffold the word so we can play
      const wordString = randomWords();
      let id = 0;
      const word = [];

      // loop over each letter of the string and give it unique id and hide the letter
      // id is meaningless just needs to be unique for vue to be happy
      for (let letter of wordString) {
        word.push({
          id: id++,
          letter: letter,
          showLetter: false
        });
      }
      this.wordArray = word;
      this.word = wordString; // to display the word at the end of the game
    },
    guessLetter(letter) {
      // guessed letters will be disabled
      this.guessed.push(letter);
      // loop over each letter and show the answer if it is in the word
      this.wordArray.map(item => {
        if (item.letter === letter) {
          item.showLetter = true;
        }
        return item;
      });
      // FoundLetter will be undefined if that letter is not in the word
      const foundLetter = this.wordArray.find(item => item.letter === letter);
      if (!foundLetter) {
        // advance the hangman closer to defeat
        this.manStage += 1;
        return;
      }
      const winner = this.wordArray.every(item => item.showLetter === true);
      if (winner) {
        this.gameWon = true;
      }
    }
  },
  watch: {
    manStage: function(val) {
      // every wrong guess advances the manStage
      // 6 is the last image and game over
      if (val >= 6) {
        this.gameOver = true;
      }
    }
  },
  mounted: function() {
    // when loading the app start the game
    this.startNewGame();
  }
};
</script>

<style>
.blank {
  margin-right: 10px;
}
/* Make it very obvious the letter was guessed and cant be selected */
.btn:disabled {
  background: #9c9c9c9c;
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
