<template>
  <h1>Peek-a-Vue</h1>
  <section class="game-board">
    <CardForEach
      v-for="(card, index) in cardList"
      :key="`card-${index}`"
      :matched="card.matched"
      :value="card.value"
      :visible="card.visible"
      :position="card.position"
      @select-card="flipCard"
    />
  </section>
  <h2>{{ status }}</h2>
  <button @click="restartGame">Restart Game</button>
</template>

<script>
import _ from 'lodash';
import { computed,ref, watch } from "vue";
import CardForEach from "./components/CardForEach";
export default {
  name: "App",
  components: {
    CardForEach,
  },
  setup() {
    const cardList = ref([]);
    const userSelection = ref([]);

    const status = computed(()=>{
      if(remainingPairs.value === 0){
        return 'Palyer wins';
      }else{
        return `Remaining Pairs: ${remainingPairs.value}`
      }
    })
    const remainingPairs = computed(()=>{
      const remainingCards = cardList.value.filter(card=>card.matched === false).length

      return remainingCards/2;
    }) 

    const shuffleCards = ()=>{
      cardList.value = _.shuffle(cardList.value)
    }
    const restartGame = ()=>{
      shuffleCards()
      cardList.value = cardList.value.map((card,index) => {
        return{
          ...card,
          matched:false,
          position:index,
          visible:false
        }
      })
    }

    const cardItems = ['bat','candy','cauldron','cupcake','ghost','moon','pumpkin','witch-hat']
    cardItems.forEach(item=>{
      cardList.value.push({
        value: item,
        visible:false,
        position: null,
        matched:false
      });
      cardList.value.push({
        value: item,
        visible:false,
        position: null,
        matched:false
      });
    })

    cardList.value = cardList.value.map((card,index)=>{
      return{
        ...card,
        position:index
      }
    })


    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true;
      if (userSelection.value[0]) {
        if(userSelection.value[0].position === payload.position && userSelection.value[0].faceValue === payload.faceValue){
          return;
        }else{
          userSelection.value[1] = payload;
        }
      } else {
        userSelection.value[0] = payload;
      }
    };

    watch(
      userSelection,
      (currValue) => {
        if (currValue.length === 2) {
          const cardOne = currValue[0];
          const cardTwo = currValue[1];

          if(cardOne.faceValue === cardTwo.faceValue){
            cardList.value[cardOne.position].matched = true;
            cardList.value[cardTwo.position].matched = true;
          }else{
            setTimeout(()=>{
              if(!cardList.value[cardOne.position].matched) cardList.value[cardOne.position].visible = false;
              if(!cardList.value[cardTwo.position].matched) cardList.value[cardTwo.position].visible = false;
            },2000)
          }
          userSelection.value.length = 0;
        }
      },
      { deep: true }
    );

    return {
      cardList,
      flipCard,
      userSelection,
      status,
      shuffleCards,
      restartGame
    };
  },
};
</script>

<style>
html,body{
  margin:0;
  padding: 0;
}
h1{
  margin-top: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-image: url('/public/images/page-bg.png');
  background-color: #00070c;
  height: 100vh;
  color: #fff;
  padding-top: 60px;
}

.game-board {
  display: grid;
  grid-template-columns: repeat(4,100px);
  grid-template-rows: repeat(4,100px);
  grid-column-gap: 24px;
  grid-row-gap: 24px;
  justify-content: center;
}
</style>
