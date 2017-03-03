<template>
  <div id="app">
    <div id="app1" :style="vis2">
      <main-cmp @GameOver="gameOver=$event"></main-cmp>
    </div>
    <div id="app2" :style="vis1">
      <rezultat-cmp></rezultat-cmp>
    </div>
      
    </div>
  </div>

</template>

<script>
  import Main from './Components/Main.vue';
  import Rezultat from './Components/Rezultat.vue';

export default {
  data: function() {
    return {
      gameOver: false,
      vis1: "visibility:hidden",
      vis2: ""
    };
  },
  
  watch: {
    gameOver: function() {
      this.vis1 = "";
      this.vis2 = "display:none";
    }
  },
  
  components: {
    "main-cmp": Main,
    "rezultat-cmp": Rezultat
  }
};

</script>
