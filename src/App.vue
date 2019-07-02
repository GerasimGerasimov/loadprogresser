//http://qaru.site/questions/14357074/how-to-get-the-element-width-and-height-immediately-when-it-resizing-in-vuejs
//выше ссылка про refs и наблюдатели

//ссылка на пример http://jsfiddle.net/Vlad_IT/qt64rouL/1/
//Чтобы стилизовать инлайн-svg, я присвоил ему id (id="sample" )
//и теперь в файле стилей могу через #sample воздействовать на него
//например #sapmle rect {} воздействует на svg->rect
<template>
  <div id="app" class="wrapper" ref="loadprogresser" @resize='onResize'>
    <svg id="sample" version="1.1" xmlns="http://www.w3.org/2000/svg"
          width="100%" height="100%">
        <rect x=0 y=0 width="100%" height="100%"/>
        <path :d="d" fill="transparent" stroke="black"/>
    </svg>
    <h2>ПРИВЕТ!</h2>
  </div>
</template>

<script>
export default {
  name: 'app',
  data(){
    return {
      d:"M 0 0",
      Samples:0,//номер текущего сэмпла
      maxSamples:400, //кол-во сэмплов в 100%-ах ширины
      wrapHeight:100,//высота в которую надо вписать изображение
      maxY:0,
      scales:{
        w:1, h:1
      },
      timePoints:[],
      TimeM:100
    }

  },
  mounted: function(){
    this.getScales()
    this.updateStar()
  },
  methods:{
    getScales(){
      const {width, height} = this.$refs.loadprogresser.getBoundingClientRect()
      this.scales.w = width / this.maxSamples
      this.wrapHeight = height
      console.log(this.scales.w, width, this.maxSamples)
    },
    onResize(){

    },
    getVScale(){
      this.scales.h = (this.maxY == 0)? 0 : this.wrapHeight / this.maxY
    },
    //передаю значения выделяю максимум и выдаю шкалу относительно этого максимума
    getYMax({value = 0}){
      if (value > this.maxY) this.maxY = value
      console.log(this.maxY)
    },
    addTime({time = 0}){
      if (this.Samples < this.maxSamples) {
        this.getYMax({value:time})
        this.getVScale()
        this.timePoints.push(time) //интересуют Int
        this.Samples ++;
        //console.log(this.Samples)
      }
    },
    getSVGTimePoints(){
      //теперь создаю строку для Path
      let predY = 0
      let path = this.timePoints.reduce((str, item)=>{
        let dY = (item - predY) * this.scales.h
        predY = item
        return str + `l ${this.scales.w} ${dY} `
      },'M 0 0 ')
      path +=`L ${this.timePoints.length * this.scales.w} 0`// Z` контур можно не замыкать
      return path
    },
    updateStar(){
      this.addTime({time:(this.TimeM*Math.random() | 0)})
      this.TimeM *= 1.01
      this.d = this.getSVGTimePoints()
      setTimeout(this.updateStar, 10)
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

.wrapper {
  border: 1px red solid;
  width: 400px;
  height: 100px;
  overflow: hidden;
}

.star {
  stroke: #005000;
  stroke-width: 4;
  fill: none;
}

</style>
