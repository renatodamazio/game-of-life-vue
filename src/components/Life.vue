<template lang="html">
  <div>
    <button @click="randomAliveCells()">Gerar células vivas</button>

    <button @click="play()">Vida!</button>

    <button @click="pause()">Pause</button>

    <button @click="reset()">Reset</button>

    <section class="grid" :style="'width:' + parseInt(width - 40) + 'px;'">
      <div v-for="(x, i) in items" class="row">
        <div v-for="(y, j) in items[i]"
          :key="(i)+'-'+(j)"
          :style="'width:' + boxSize + 'px; height' + boxSize + 'px'"
          :class="'box alive-' + items[i][j].status" @click="HandGiveLife(i, j)">
        </div>
      </div>
    </section>
    <div v-if="end">
        <h2>Game Over</h2>
    </div>
    <ul>
      <li>População: {{alives}}</li>
      <li>Geração: {{generation}}</li>
    </ul>
  </div>
</template>

<script>
import Vue from 'vue';

export default {
  name: 'Life',
  data() {
    return {

      width: 140,
      items: [],
      rows: 20,
      cols: 40,
      boxSize: 14,
      interval: '',
      alives: 0,
      end: false,
      deaths: 0,
      generation: 0

    }
  },

  watch: {
    items: {
      handler(old) {

        this.general();

      },
      deep: true
    }
  },

  mounted() {
     this.width = parseInt(this.cols * this.boxSize);
     this.createGrid();
   },

  methods: {

    play() {

      this.deaths = 0;
      this.end = false;
      clearInterval(this.interval);
      this.interval = setInterval(this.Life, 90)
    },

    pause() {
      clearInterval(this.interval);
    },

    reset() {
      clearInterval(this.interval);
      this.end = false;
      this.generation = 0;
      this.alives = 0;
      this.createGrid();
    },

    gameOver() {
      if(this.alives === 0) {
        this.end = true;
        clearInterval(this.interval);
      }
    },

    general() {
      this.alives = 0;
      for (var x = 0; x <= this.rows -1; x++) {
        for (var y = 0; y <= this.cols -1; y++) {
          var total = Number(this.getNeighborsAlive(x, y)).toFixed(3);
            this.alives += (total/8);
        }
      };
    },

    randomAliveCells() {
      for (var x = 0; x <= this.rows - 1; x++) {
        for (var y = 0; y <= this.cols - 1; y++) {
          if(Math.floor(Math.random() * 4) === 0) {
              if(this.items[x][y]){
                this.items[x][y].status = true;
              }
          }
        }
      }
      this.general();
    },

    HandGiveLife(row, col) {

      var items = this.items[row][col];
      var status = items.status;
      if(status == 0) { status = true } else { status = false };
      Vue.set(items, 'status', status);

    },

    createGrid() {

      var $this = this;
      for (var x = 0; x <= this.rows -1; x++) {
        Vue.set(this.items, [x], []);
        for (var y = 0; y <= this.cols -1; y++) {
          Vue.set(this.items[x],[y], {

            status: false,
            x: x,
            y: y,
            neighbors: this.getNeighbors(x, y)

          });
        };
      };
    },

    getNeighborsAlive(i, j) {

      var $this = this;
      var total = this.items[i][j].neighbors.filter(function(coord){

        var x = coord[0], y = coord[1];
        return ($this.items[x][y].status);

      }).length;

      return total;

    },

    getNeighbors(i, j) {

      var neighbors = [];
      var rowLimit = this.rows - 1;
      var columnLimit = this.cols - 1;

       for(var x = Math.max(0, i-1); x <= Math.min(i+1, rowLimit); x++) {
         for(var y = Math.max(0, j-1); y <= Math.min(j+1, columnLimit); y++) {
           if(x !== i || y !== j) {
             neighbors.push([x, y]);
           }
         }
       }
       return neighbors;
     },

     Life() {

       var $this = this;
       var grid = [];

       for (var x = 0; x <= this.rows -1; x++) {
         grid[x] = []
         for (var y = 0; y <= this.cols -1; y++) {
           grid[x][y] = this.getNeighborsAlive(x, y);
         }
       }

       for (var x = 0; x <= this.rows -1; x++) {
         for (var y = 0; y <= this.cols -1; y++) {
           if(grid[x][y]==3 && !this.items[x][y].status) {
             this.items[x][y].status = true;
           }
           if(this.items[x][y].status && (grid[x][y]==3 || grid[x][y]==2)) {
             this.items[x][y].status = true;
           }
           if(grid[x][y]<2 && this.items[x][y].status) {
             this.items[x][y].status = false;
           }
           if(grid[x][y]>3) {
             this.items[x][y].status = false;
           }
         }
       };
       this.generation+=1;
       this.gameOver();
     }
  }

}
</script>

<style lang="css">
.box{
  color: #000 !important;
  line-height: 15px;
}
.alive-true{
  background: green;
  color: #fff;
}
.alive-false{
  background: #fff;
  color: #000;
}
.alive-red{
  background: red;
  color: #fff;
}
</style>
