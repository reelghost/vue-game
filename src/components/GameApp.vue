<template>
    <div class="game-containerz min-h-[40%] min-w-[50%] bg-gradient-to-br from-black to-red-600 p-6 rounded-lg shadow-lg max-w-md mx-auto relative">
      <div class="text-center mb-4">
        <h2 class="text-4xl font-bold bg-gradient-to-r from-amber-500 to-pink-500 bg-clip-text text-transparent mb-2">Guess The Word</h2>
      </div>
      <div class="text-center mb-2">
        <h2 class="text-base font-semibold mb-2">HINT: <span class="font-normal">{{ hint }}</span></h2>
      </div>
      <div class="text-center mb-4">
        <h1 class="text-2xl tracking-widest bg-gradient-to-r from-stone-100 to-pink-500 bg-clip-text text-transparent">{{ displayWord }}</h1>
      </div>
      <div class="text-center">
        <h3 class="text-base font-medium mb-2">Letters guessed👇</h3>
        <div class="">
          <span class="text-sm lowercase text-neutral-300" v-for="(comment, index) in comments" :key="index">
            {{ comment }}<span v-if="index < comments.length - 1">, </span>
          </span>
        </div>
      </div>
      <transition name="fireworks" v-if="isWordGuessed">
        <div class="celebration-overlay">
          <div class="firework" v-for="(firework, index) in fireworks" :key="index"></div>
          <div class="modal">
            <h1>🎉✨🎉Congratulations! We won🎉✨🎉</h1>
          </div>
        </div>
      </transition>
    </div>
</template>

<script>
  import { generate } from 'random-words';
  import axios from 'axios';

  export default {
    data() {
      return {
        word: '',
        clue: '',
        guessedLetters: [],
        comments: [],
        isWordGuessed: false,
        fireworks: [],
      };
    },
    computed: {
      displayWord() {
        return this.word
          .split('')
          .map(letter => (this.guessedLetters.includes(letter.toUpperCase()) ? `${letter}` : '_'))
          .join(' ');
      },
    },
    mounted() {
      this.startNewWord();
      window.addEventListener('keyup', this.handleKeyup);
    },
    beforeUnmount() {
      window.removeEventListener('keyup', this.handleKeyup);
    },
    methods: {
      handleKeyup(event) {
        const letter = event.key.toUpperCase();
        if (/[A-Z]/.test(letter) && !this.guessedLetters.includes(letter)) {
          this.guessedLetters.push(letter);
          this.comments.push(letter);
          if (this.isWordFullyGuessed()) {
            this.isWordGuessed = true;
            setTimeout(() => {
              this.isWordGuessed = false;
              this.startNewWord();
            }, 5000);
          }
        }
      },
      isWordFullyGuessed() {
        return this.word.split('').every(letter => this.guessedLetters.includes(letter.toUpperCase()));
      },
      async startNewWord() {
        const randomWord = generate({ exactly: 1, maxLength: 8 })[0];
        this.word = randomWord.toUpperCase();
        await this.fetchWordDefinition(randomWord);
        this.guessedLetters = [];
        this.comments = [];
        this.fireworks = [];
        this.createFireworks();
      },
      async fetchWordDefinition(word) {
        try {
          const response = await axios.get(`https://api.dictionaryapi.dev/api/v2/entries/en/${word}`);
          const definitions = response.data[0]?.meanings[0]?.definitions[0]?.definition;
          this.hint = `${definitions}`;
        } catch (error) {
          console.error('Error fetching word definition:', error);
          this.hint = `The word has ${this.word.length} letters`;
        }
      },
      createFireworks() {
        for (let i = 0; i < 10; i++) {
          this.fireworks.push({
            top: `${Math.random() * 100}%`,
            left: `${Math.random() * 100}%`,
            width: `${Math.random() * 100}px`,
            height: `${Math.random() * 100}px`,
            animationDelay: `${Math.random() * 5}s`,
          });
        }
      },
    },
  };
</script>

<style scoped>
  .game-container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    border: 1px solid #ccc;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    position: relative;
    z-index: 1;
  }

  .clue-section h2 {
    margin-bottom: 20px;
  }

  .word-display h1 {
    letter-spacing: 10px;
  }

  .comments-section {
    margin-top: 20px;
    text-align: left;
  }

  .comments-row {
    display: flex;
    flex-wrap: wrap;
  }

  .comments-row span {
    margin-right: 4px;
  }

  .celebration-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 10;
    animation: fadeIn 1s;
  }

  .modal {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: rgba(0, 0, 0, 0.7);
    color: white;
    padding: 20px;
    border-radius: 10px;
    text-align: center;
  }

  .firework {
    position: absolute;
    background: radial-gradient(circle, #ffffff 0%, #ffffff 5%, transparent 40%);
    width: 10px;
    height: 10px;
    border-radius: 50%;
    animation: fireworks 3s infinite;
  }

  @keyframes fireworks {
    0% {
      transform: translateY(0) scale(1);
    }
    50% {
      transform: translateY(-100px) scale(2);
      opacity: 1;
    }
    100% {
      transform: translateY(-200px) scale(1);
      opacity: 0;
    }
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
</style>
