//ссылка на пример http://jsfiddle.net/Vlad_IT/qt64rouL/1/
//Чтобы стилизовать инлайн-svg, я присвоил ему id (id="sample" )
//и теперь в файле стилей могу через #sample воздействовать на него
//например #sapmle rect {} воздействует на svg->rect
<template>
  <div id="app">
    <svg id="sample" version="1.1" xmlns="http://www.w3.org/2000/svg" width="500px" height="400px">
        <rect x=0 y=0 width="500px" height="400px"/>
        <path :d="d" fill="transparent" stroke="black"/>
        <polygon class="star" :points="points" transform="translate(50 50) "/>
    </svg>
    <h2>ПРИВЕТ!</h2>
  </div>
</template>

<script>
export default {
  name: 'app',
  data(){
    return {
      //xy:[[255.1,1.1],  [240.4,31.4], [206.6,36.1], [230.9,61.1], [225.1,93.9],
      //    [255.1,78.4], [284.4,93.9], [279.4,60.1], [303.6,36.4], [270.1,31.6]],
      xy:[[0,0],  [0,50], [50,50], [50,0]],          
      points: "",
      d:"M 0 0",
      angle:0, //угол поворота
      width:400, //ширина в которую надо вписать изображение
      height:100,//высота в которую надо вписать изображение
      timePoints:[]
    }

  },
  created: function () {
    this.updateStar()
  },
  methods:{
    addTime:function ({time = 0}){
      this.timePoints.push(time) //интересуют Int
    },
    getSVGTimePoints:function(){
      //теперь создаю строку для Path
      let predY = 0
      let path = this.timePoints.reduce((str, item)=>{
        let dY = item - predY
        predY = item
        return str + `l 1 ${dY} `
      },'M 0 0 ')
      path +=`L ${this.timePoints.length} 0`// Z` контур можно не замыкать
      return path
    },
    updateStar:function(){
      this.addTime({time:(100*Math.random() | 0)})
      this.d = this.getSVGTimePoints()
      const rad = Math.PI/180
      //увеличу все координаты на 1
      setTimeout(this.updateStar, 10)
      let xy = this.xy.map((item)=>{
        let ra = this.angle
        let x = 0
        let y = 0
        x = item[0] * Math.cos(ra) - item[1] * Math.sin(ra)
        y = item[0] * Math.sin(ra) + item[1] * Math.cos(ra)
        item[0] = x
        item[1] = y
        //item[0] +=0.1
        //item[1] +=0.1
        return item
      })
      //теперь из пар xy сделаю строку 
      let points = xy.reduce((sum, item)=>{
        return sum+`${item[0]},${item[1]} `
      },'')
      //console.log(points)
      this.points = points
      this.angle +=0.0001
      if (this.angle > 0.1) this.angle = 0
      //console.log(this.angle)
    }
  },
  components: {
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
