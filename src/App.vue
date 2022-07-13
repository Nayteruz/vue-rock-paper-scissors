<template>
  <div class="bg-gray-700 text-white text-center min-h-screen flex flex-col">
    <header class="container mx-auto p-6">
      <h1 class="text-4xl font-bold">Камень, Ножницы, Бумага</h1>
    </header>

    <main class="container mx-auto p-6 flex-l">
      <div v-if="choice === null" class="flex items-center justify-center -mx-6">
        <button
          @click="play('rock')"
          class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration-300 hover:bg-pink-500"
        >
          <img src="./assets/RockIcon.svg" alt="Камень" class="w-full">
        </button>
        <button
            @click="play('paper')"
            class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration-300 hover:bg-green-500"
        >
          <img src="./assets/PaperIcon.svg" alt="Бумага" class="w-full">
        </button>
        <button
            @click="play('scissors')"
            class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration-300 hover:bg-yellow-500"
        >
          <img src="./assets/ScissorIcon.svg" alt="Ножницы" class="w-full">
        </button>
      </div>

      <div v-else>
        <div class="text-3xl mb-4">
          Вы выбрали <span class="text-pink-500">{{ ruChoice[choice] }}</span>
        </div>
        <div class="text-3xl mb-4">
          Компьютер выбрал <span class="text-green-500">{{ ruChoice[computerChoice] }}</span>
        </div>
        <div class="text-6xl mb-12">
          {{ verdict }}
        </div>
        <button @click="ResetRound" class="bg-pink-500 text-lg py-2 px-4">Попробовать еще раз!</button>
      </div>
      <div class="mt-12 text-3xl mb-4">
        {{ wins }} : {{ draws }} : {{ losses }}
      </div>
      <div class="text-lg">
        Процент побед: {{ Math.round(winPercetage) }}%
      </div>
    </main>
  </div>
</template>

<script setup>
  import { ref, computed, onMounted } from "vue";

  const wins = ref(0);
  const draws = ref(0);
  const losses = ref(0);

  const choice = ref(null);
  const computerChoice = ref(null);
  const verdict = ref(null);

  const ruChoice = ref({
    rock: 'камень',
    paper: 'бумага',
    scissors: 'ножницы'
  })

  const outcomes = {
    rock: {
      rock: 'draw',
      paper: 'loss',
      scissors: 'win'
    },
    paper: {
      rock: 'win',
      paper: 'draw',
      scissors: 'loss'
    },
    scissors: {
      rock: 'loss',
      paper: 'win',
      scissors: 'draw'
    }
  }

  const winPercetage = computed(() => {
    const total = wins.value + draws.value + losses.value;
    return total ? (wins.value / total) * 100 : 0
  })

  const play = c => {
    choice.value = c;
    const choices = ['rock', 'paper', 'scissors'];
    const random = Math.floor(Math.random() * choices.length)
    computerChoice.value = choices[random];

    const outcome = outcomes[c][computerChoice.value];

    if(outcome === 'win') {
      wins.value++
      verdict.value = 'Ты победил!'
    } else if (outcome === 'loss'){
      losses.value++
      verdict.value = 'Ты проиграл!'
    } else {
      draws.value++
      verdict.value = 'Это ничья!'
    }

    SaveGame();
  }

  const SaveGame = () => {
    let scores = {
      wins: wins.value,
      losses: losses.value,
      draws : draws.value
    }
    localStorage.setItem('score', JSON.stringify(scores));
  }

  const LoadGame = () => {
    let scores = JSON.parse(localStorage.getItem('score'));
    wins.value = parseInt(scores?.wins ?? 0);
    losses.value = parseInt(scores?.losses ?? 0);
    draws.value = parseInt(scores?.draws ?? 0);
  }

  const ResetRound = () => {
    choice.value = null;
    computerChoice.value = null;
    verdict.value = null;
  }

  onMounted(()=> {
    LoadGame();
    window.addEventListener('keypress', e => {
      if(e.key === 'r') {
        ResetRound();
      }
    })
  })

</script>
