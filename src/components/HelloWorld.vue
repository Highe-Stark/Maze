<template>
  <div class="maze">
    <h1>{{ msg }}</h1>
    <canvas id="board" height="800px" width="800px" style="border:2px solid #42b983"></canvas>
    </br>
    </br>
    <span>
      <p id="au">{{ bottom }} </p>
      <p id="po">{{ powered }}
        <img src="./../assets/logo.png" height="25px" width="25px"/>
      </p>
    </span>
  </div>
</template>

<script>
export default {
  name: 'Maze',
  data () {
    return {
      msg: 'Welcome to Maze world.',
      bottom: '------ Made By Higher Stark ------- ',
      powered: 'Powered By '
    }
  }
}
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
  color:#42b983;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#au {
  text-align:center;
  color:aquamarine;
  font-size:1.4rem;
}
#po {
  text-align: right;
  color:#42b983;
  font-size:1.1rem;
}
</style>
