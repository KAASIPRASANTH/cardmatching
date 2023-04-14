<template>
  <h1>Peek-a-Vue</h1>
  <section class="game-board">
      <CardForEach 
      v-for="(card,index) in cardList"
      :key="`card-${index}`"
      :value="card.value"
      :visible="card.visible"
      :position="card.position"
      @select-card="flipCard"
      />
  </section>
  <h2>{{ userSeletion }}</h2>
</template>

<script>
import {ref} from 'vue'
import CardForEach from './components/CardForEach'
export default {
  name:'App',
  components:{
    CardForEach
  },
  setup() {
    const cardList = ref([])
    const userSeletion = ref([])


    for(let i=0;i<16;i++){
      cardList.value.push({
        value:i,
        visible:false,
        position:i
      })
    }
    const flipCard = payload =>{
      cardList.value[payload.position].visible = true
      if(userSeletion.value[0]){
        userSeletion.value[1] = payload;
      }else{
        userSeletion.value[0] = payload;
      }
    }
    return{
      cardList,
      flipCard,
      userSeletion
    }
  }
}
</script>


<style>
#app{
  font-family: Avenir, Helvetica, Arial,sans-serif;
  -webkit-font-smoothing:antialiased;
  -moz-osx-font-smoothing:grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.card{
  border: 5px solid #ccc;
}
.game-board{
  display: grid;
  grid-template-columns: 100px 100px 100px 100px;
  grid-template-rows: 100px 100px 100px 100px;
  grid-column-gap: 30px;
  grid-row-gap:30px;
  justify-content: center;
}
</style>
