<template>
  <div class="hello">
  <table>
    <tr v-bind:key="row.id" v-for="row in board">
      <td v-bind:key="cell.id" v-for="cell in row">{{ cell }}</td>
    </tr>
  </table>
  </div>
</template>

<script>
export default {
  name: 'GameComponent',
  data: function(){
    return {
        board: [
          ['','',''],
          ['','',''],
          ['','',''],
          // ['X','X','X'],
          // ['X','X','X'],
          // ['X','X','X'],
        ]
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
</style>
