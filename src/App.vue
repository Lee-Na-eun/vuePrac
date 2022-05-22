<template>
  <h1>Peek-a-Vue</h1>
  <transition-group tag="section" className="game-board" name="shuffle-card">
    <Card
      v-for="card in cardList"
      :key="`${card.value}-${card.variant}`"
      :value="card.value"
      :visible="card.visible"
      :position="card.position"
      :matched="card.matched"
      @select-card="flipCard"
    />
  </transition-group>
  <h2>{{ status }}</h2>
  <button v-if="newPlayer" @click="startGame">Start Game</button>
  <button v-else @click="restartGame">Restart Game</button>
</template>

<script>
import _ from 'lodash';
import { computed, ref, watch } from 'vue';
import Card from './components/Card.vue';
import { launchConfetti } from './utilities/confetti';
export default {
  name: 'App',
  components: {
    Card,
  },
  setup() {
    const cardList = ref([]);
    const useSelection = ref([]);
    const newPlayer = ref(true);
    const startGame = () => {
      newPlayer.value = false;

      restartGame();
    };
    const status = computed(() => {
      if (remainingPairs.value === 0) {
        return 'Player Wins!';
      } else {
        return `RemainignPairs : ${remainingPairs.value}`;
      }
    });

    const restartGame = () => {
      cardList.value = _.shuffle(cardList.value);

      cardList.value = cardList.value.map((card, index) => {
        return { ...card, matched: false, visible: false, position: index };
      });
    };

    const remainingPairs = computed(() => {
      const remainingCards = cardList.value.filter(
        (card) => card.matched === false
      ).length;

      console.log(remainingCards);

      return remainingCards / 2;
    });

    const cardItems = [
      'apple',
      'banana',
      'cherry',
      'grape',
      'strawberry',
      'tomato',
      'melon',
      'orange',
    ];

    cardItems.forEach((item) => {
      cardList.value.push({
        value: item,
        visible: false,
        variant: 1,
        position: null,
        matched: false,
      });

      cardList.value.push({
        value: item,
        visible: false,
        variant: 2,
        position: null,
        matched: false,
      });
    });

    cardList.value = cardList.value.map((card, index) => {
      return {
        ...card,
        position: index,
      };
    });

    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true;

      if (useSelection.value[0]) {
        if (
          useSelection.value[0].position === payload.position &&
          useSelection.value[0].faceValue === payload.faceValue
        ) {
          return;
        } else {
          useSelection.value[1] = payload;
        }
      } else {
        useSelection.value[0] = payload;
      }
    };

    watch(remainingPairs, (currentValue) => {
      if (currentValue === 0) {
        launchConfetti();
      }
    });

    watch(
      useSelection,
      (currentValue) => {
        if (currentValue.length === 2) {
          const cardOne = currentValue[0];
          const cardTwo = currentValue[1];

          if (cardOne.faceValue === cardTwo.faceValue) {
            cardList.value[cardOne.position].matched = true;
            cardList.value[cardTwo.position].matched = true;
          } else {
            setTimeout(() => {
              cardList.value[cardOne.position].visible = false;
              cardList.value[cardTwo.position].visible = false;
            }, 2000);
          }
          useSelection.value.length = 0;
        }
      },
      { deep: true }
    );

    return {
      cardList,
      flipCard,
      useSelection,
      status,
      restartGame,
      startGame,
      newPlayer,
    };
  },
};
</script>

<style>
body,
html {
  margin: 0;
  padding: 0;
}

h1 {
  margin-top: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
  background-color: black;
  height: 100vh;
  padding-top: 60px;
}

.game-board {
  display: grid;
  grid-template-columns: repeat(4, 120px);
  grid-template-rows: repeat(4, 120px);
  grid-column-gap: 24px;
  grid-row-gap: 24px;
  justify-content: center;
}

button {
  width: 120px;
  height: 40px;
  background-color: white;
  border: none;
  color: purple;
  cursor: pointer;
}

.shuffle-card-move {
  transition: transform 0.8s ease-in;
}
</style>
