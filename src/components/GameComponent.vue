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
      <!-- Using sound: <td @click.prevent="playSound(soundPath)"  -->
    </tr>
    </table>
    <p v-if="this.winner !== 'XOX' && this.isDone">{{this.gameOverMessage}} {{this.winner}}</p>
    <p v-if="this.winner === 'XOX' && this.isDone">Game is over</p>
    <p v-if="this.isDone">Restart with:</p>
    <button v-if="this.isDone && this.winner === 'O'" @click="restart('easy')"> Easy </button>
    <button v-if="this.isDone && this.winner !== 'O'" @click="restart('normal')"> Normal </button>
  </div>
</template>

<script>
export default {
  name: 'GameComponent',
  data: function(){
    return {
        isDone: false,
        gameOverMessage : "Game is over. The winner is ",
        winner: '',
        difficulty: 'easy',
        turn: 'X',
        // soundPath: 'http://soundbible.com/mp3/Elevator Ding-SoundBible.com-685385892.mp3',
        animation: true,
        board: [
          ['','',''],
          ['','',''],
          ['','',''],
        ]
    }
  },
  methods:{
    // For sound 
    // playSound: function(sound) {
    //   if(sound) {
    //     var audio = new Audio(sound);
    //     audio.play();
    //   }
    // },
  
    clicked: function(x,y){
      this.markX(x,y)
      this.isDoneF() ? this.turn = '' : this.computerMove()
      this.isDoneF() ? this.turn = '' : null 
    },

    computerMove: function(){
      let tryAgainCount = 0;
      while(this.turn === 'O'){
        let randindexX = Math.floor( Math.random() * 3)
        let randindexY = Math.floor( Math.random() * 3)
        if(this.difficulty === 'easy'){
          if(this.board[randindexX][randindexY] === ''){
            this.board[randindexX][randindexY] = 'O'
            this.turn = 'X'
          } 
        }
        else if(this.difficulty === 'normal'){
          if(this.board[randindexX][randindexY] === ''){
            this.board[randindexX][randindexY] = 'O'
            if(this.isDoneF() || tryAgainCount >= 20){
              this.isDone = false //to check in inDoneF after that function
              this.turn = 'X'
            } else{
                this.board[randindexX][randindexY] = ''
                this.turn = 'O'
                tryAgainCount += 1 
              }
          }           
        } 
      } //end of while
    this.$forceUpdate()
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
        } //End of for
      return this.eachBoxFilled()
      } //End of if
    },
    //Restart button
    restart: function(diff) {
      this.board = [['','',''],['','',''],['','','']]
      this.turn = 'X'
      this.winner = ''
      this.difficulty = diff
      this.isDone = false
    },

    //Mark by finger X
    markX: function(x,y){
      if(this.turn === 'X' && this.board[x][y] === '' && !this.isDone){
        this.board[x][y] = 'X' 
        this.turn = 'O' 
        this.$forceUpdate()
      }
    },

    // EACH BOX FILLED
    eachBoxFilled: function(){
      let filledBoxCounter = 0
      let i = 0
      let j = 0
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
      } else {
        return false 
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
  color: #0a122e;
  border-collapse: collapse;
}

table tr:nth-child(1),
table tr:nth-child(2){
  border-bottom: 1px solid #0a122e;
}

table td:nth-child(1),
table td:nth-child(2){
  border-right: 1px solid #0a122e;
}

table td{
  font-weight: 900;
  cursor: pointer;
  height: 33.3%;
  width: 33.3%;
}

p{
  margin-top: 16px;
  font-weight: 900;
}

button{
  background-color: #0a122e;
  border-radius: 5px;
  font-weight: 900;
  /* text-transform: uppercase; */
  border: 0;
  padding: 10px 20px;
  color: white;
  cursor: pointer;
}

button:hover{
  background-color: #0e1c50;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter, .fade-leave-to  {
  opacity: 0;
}

</style>
