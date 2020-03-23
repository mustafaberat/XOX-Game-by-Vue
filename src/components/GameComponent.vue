<template>
  <div class="container">
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
    <p v-if="!this.isDone">{{this.difficulty}}</p>
    <p v-if="this.winner !== 'XOX' && this.isDone">The winner is {{this.winner}}</p>
    <p v-if="this.winner === 'XOX' && this.isDone">Game over</p>
    <p v-if="this.isDone">Restart with:</p>
    <button v-if="this.isDone" @click="restart('easy')"> Easy </button>
    <button v-if="this.isDone" @click="restart('normal')"> Normal </button>
    <button v-if="this.isDone" @click="restart('hard')"> Hard </button>
    <div class="diffinfo" v-if="this.isDone">
      <i>Easy: Plays randomly</i>
      <i>Normal: Does not miss opportunities</i>
      <i>Hard: Prevents Finishing</i>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameComponent',
  data: function(){
    return {
        isDone: false,
        winner: '',
        difficulty: 'easy',
        dontLookCounter : 0,
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
      this.diffEasy()
      this.diffNormal()        
      this.diffHard()        
      this.$forceUpdate()
    },

    diffHard: function() {
      if(this.difficulty === 'hard'){
        let tryAgainCount = 0;
        while(this.turn === 'O'){
        let randindexX = Math.floor( Math.random() * 3)
        let randindexY = Math.floor( Math.random() * 3)
          
          if(this.board[randindexX][randindexY] === ''){
            this.board[randindexX][randindexY] = 'O'
            if(this.dontLookCounter < 1){
              this.dontLookCounter += 1
              this.isDone = false //to check in inDoneF after that function
              this.turn = 'X'
            }
            else if(this.isDoneF()){ // O can finish 
              this.isDone = false //to check in inDoneF after that function
              this.turn = 'X'
            } 
            else if(tryAgainCount >= 20 && tryAgainCount <= 40){ //Check X can finish, avoid[O cant finish]
                this.board[randindexX][randindexY] = 'X' //Put it X on some point
                this.turn = 'O'
                if(this.isDoneF()){ //X can finish
                  this.isDone = false
                  this.board[randindexX][randindexY] = 'O' //Avoid that point
                  this.turn = 'X'
                } else{ //X can not finish
                    this.board[randindexX][randindexY] = ''
                    this.turn = 'O'
                    tryAgainCount += 1 
                }
            }
            else if(tryAgainCount > 40){ //Put in random box. Go outloop anymore
              this.turn = 'X' 
            } 
            else{
              this.board[randindexX][randindexY] = ''
              this.turn = 'O'
              tryAgainCount += 1 
            }
          }//End of empty box if
        } //end of while
      }
    },

    diffNormal: function() {
      if(this.difficulty === 'normal'){
        let tryAgainCount = 0;
        while(this.turn === 'O'){
        let randindexX = Math.floor( Math.random() * 3)
        let randindexY = Math.floor( Math.random() * 3)
          if(this.board[randindexX][randindexY] === ''){
            this.board[randindexX][randindexY] = 'O'
            if(this.isDoneF() || tryAgainCount >= 20 || this.dontLookCounter < 2){ //Won or no solution
              this.dontLookCounter += 1
              this.isDone = false //to check in inDoneF after that function
              this.turn = 'X'
            } else{
                this.board[randindexX][randindexY] = ''
                this.turn = 'O'
                tryAgainCount += 1 
              }
          }
        } //end of while
      }
    },
    
    diffEasy: function(){
        if(this.difficulty === 'easy'){
          while(this.turn === 'O'){
          let randindexX = Math.floor( Math.random() * 3)
          let randindexY = Math.floor( Math.random() * 3)
          if(this.board[randindexX][randindexY] === ''){
            this.board[randindexX][randindexY] = 'O'
            this.turn = 'X'
          } 
        }
      }
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
      this.dontLookCounter = 0
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
  margin-left: 5px;
  margin-right: 5px;
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

.diffinfo{
  display: none;
  grid-template-columns:auto auto auto; 
  align-items: center;
  justify-content: space-evenly;
  margin-top: 40px;
}

@media (min-width: 700px){
  .diffinfo{
      display: grid;
    }
}

@media (max-width: 300px) {
  .container {
    font-size: 0.8rem;
  }
  table{
    width: 250px;
    height: 250px;
    font-size: 30px;
  }
  button{
    margin-top: 5px;
    font-size: 0.8rem;
  }
}

@media (max-width: 270px) {
  .container {
    font-size: 0.8rem;
  }
  table{
    width: 220px;
    height: 200px;
  }
  button{
    margin-top: 5px;
    font-size: 0.7rem;
  }
}

</style>
