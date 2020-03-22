<template>
  <div>
    <table cellspacing= "0">
    <tr>
      <td @click="clicked(0,0)"><transition name="fade"><span v-if="this.board[0][0]">{{this.board[0][0]}} </span></transition></td>
      <td @click="clicked(0,1)"><transition name="fade"><span v-if="this.board[0][1]">{{this.board[0][1]}} </span></transition></td>
      <td @click="clicked(0,2)"><transition name="fade"><span v-if="this.board[0][2]">{{this.board[0][2]}} </span></transition></td>
    </tr>
     <tr>
      <td @click="clicked(1,0)"><transition name="fade"><span v-if="this.board[1][0]">{{this.board[1][0]}} </span></transition></td>
      <td @click="clicked(1,1)"><transition name="fade"><span v-if="this.board[1][1]">{{this.board[1][1]}} </span></transition></td>
      <td @click="clicked(1,2)"><transition name="fade"><span v-if="this.board[1][2]">{{this.board[1][2]}} </span></transition></td>
    </tr>
     <tr>
      <td @click="clicked(2,0)"><transition name="fade"><span v-if="this.board[2][0]">{{this.board[2][0]}} </span></transition></td>
      <td @click="clicked(2,1)"><transition name="fade"><span v-if="this.board[2][1]">{{this.board[2][1]}} </span></transition></td>
      <td @click="clicked(2,2)"><transition name="fade"><span v-if="this.board[2][2]">{{this.board[2][2]}} </span></transition></td>
    </tr>
    </table>
    <p v-if="this.isDone">{{this.gameOverMessage}} {{this.winner}}</p>
    <button v-if="this.isDone" @click="restart()"> RESTART </button>
  </div>
</template>

<script>
export default {
  name: 'GameComponent',
  data: function(){
    return {
        isDone: false,
        gameOverMessage : "Game is over. The winner is: ",
        winner: '',
        turn: 'X',
        animation: true,
        board: [
          ['','',''],
          ['','',''],
          ['','',''],
        ]
    }
  },
  methods:{
    restart: function() {
      this.board = [['','',''],['','',''],['','','']]
      this.turn = 'X'
      this.winner = ''
      this.isDone = false
    },
    clicked: function(x,y){
      this.markX(x,y)
      this.isDoneF() ? this.turn = '' : this.computerMove()
      this.isDoneF() ? this.turn = '' : null
    },

    computerMove: function(){
      setTimeout(()=>{
        if(this.turn === 'O'){
        /*eslint no-constant-condition: ["error", { "checkLoops": false }]*/
        while(true){
          let randindexX = Math.floor( Math.random() * 3)
          let randindexY = Math.floor( Math.random() * 3)
          if(this.board[randindexX][randindexY] === ''){
            this.board[randindexX][randindexY] = 'O'
            this.turn = 'X'
            break
          } 
        }
      }
      this.$forceUpdate()
      },500);
    },

    eachBoxFilled: function(){
      let filledBoxCounter = 0
      let i = 0
      let j = 0
    // EACH BOX FILLED
      for (i = 0; i < 3; i++) {
        for (j = 0; j < 3; j++) {
          if(this.board[i][j] !== ''){
            filledBoxCounter += 1
          }
        }
      }
      if(filledBoxCounter === 9) {
        this.winner = 'XOX'
        this.isDone = true
        return true
      } else return false
    },

    isDoneF: function(){
      if(!this.isDone){
        for (let i = 0; i < 3; i++) {
        // HORIZONTAL
        if(this.board[i][0]!=='' && this.board[i][1]!=='' && this.board[i][2]!=='' && this.board[i][0] === this.board[i][1] && this.board[i][1] === this.board[i][2] ){
          this.isDone = true
          this.winner = this.turn === 'O' ? 'X' : 'O'
          return true
        }
        // VERTICAL
        else if(this.board[0][i]!=='' && this.board[1][i]!=='' && this.board[2][i]!=='' && this.board[0][i] === this.board[1][i] && this.board[1][i] === this.board[2][i] ){
          this.isDone = true
          this.winner = this.turn === 'O' ? 'X' : 'O'
          return true
        }
        // CROSS left top to right bottom
        else if(this.board[i][i]!=='' && this.board[0][0] === this.board[1][1] && this.board[1][1] === this.board[2][2] ){
          this.isDone = true
          this.winner = this.turn === 'O' ? 'X' : 'O'
          return true
        }
        // CROSS right top to left bottom
        else if(this.board[0][2]!=='' && this.board[1][1]!=='' && this.board[2][0]!=='' && this.board[0][2] === this.board[1][1] && this.board[1][1] === this.board[2][0] ){
          this.isDone = true
          this.winner = this.turn === 'O' ? 'X' : 'O'
          return true
        }
      }
      return this.eachBoxFilled()
    }
  },

    markX: function(x,y){
      if(this.turn === 'X' && this.board[x][y] === '' ){
        this.board[x][y] = 'X' 
        this.turn = 'O' 
        this.$forceUpdate()
      }
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
table{
  margin-left: auto;
  margin-right: auto;
  text-align: center;
  width: 300px;
  height: 300px;
  table-layout: fixed;
  font-size: 2.5rem;
  color: #304047;
  border-collapse: collapse;
}

table tr:nth-child(1),
table tr:nth-child(2){
  border-bottom: 1px solid #304047;
}

table td:nth-child(1),
table td:nth-child(2){
  border-right: 1px solid #304047;
}

table td{
  font-weight: 900;
  height: 33.3%;
  width: 33.3%;
}

p{
  margin-top: 30px;
  font-weight: 900;
}

button{
  background-color: #304047;
  border-radius: 5px;
  border: 0;
  padding: 10px 20px;
  color: white;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter, .fade-leave-to  {
  opacity: 0;
}

</style>
