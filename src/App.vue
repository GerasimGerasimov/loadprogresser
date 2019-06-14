//ссылка на пример http://jsfiddle.net/Vlad_IT/qt64rouL/1/
//Чтобы стилизовать инлайн-svg, я присвоил ему id (id="sample" )
//и теперь в файле стилей могу через #sample воздействовать на него
//например #sapmle rect {} воздействует на svg->rect
<template>
  <div id="app">
    <svg id="sample" version="1.1" xmlns="http://www.w3.org/2000/svg" width="500px" height="400px">
        <rect x=0 y=0 width="500px" height="400px"/>
        <polygon class="star" :points="points"/>
    </svg>
    <h2>ПРИВЕТ!</h2>
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  data(){
    return {
      points: "255.1,1.1 240.4,31.4 206.6,36.1 230.9,61.1 225.1,93.9"+
              " 255.1,78.4 284.4,93.9 279.4,60.1 303.6,36.4 270.1,31.6"
    }

  },
  created: function () {
    this.updateStar()
  },
  methods:{
    updateStar:function(){
      console.log('STARTED')
      setTimeout(this.updateStar, 1000)

    }
  },
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#sample rect {
	fill: yellow;
	stroke: green;
	stroke-width: 4;
	transition: all 350ms;
}

#sample rect:hover {
	fill: magenta;
}

.star {
  stroke: #005000;
  stroke-width: 4;
  fill: none;
}

</style>
