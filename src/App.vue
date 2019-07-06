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
        <g :transform="transform">
          <path :d="d"/>
        </g>
        <!--text x="50%" y="50%" dominant-baseline="central" text-anchor="middle">{{TextPrc}}</text -->
    </svg>
    <h2>{{TextPrc}}</h2>
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
      TimeM:100,
      transform:'scale( 1, -1) translate(0,0) rotate(0)',
      TextPrc: '0%'
    }

  },
  mounted: function(){
    this.getScales()
    this.updateStar()
  },
  methods:{
    getScales(){
      console.log('getScale')
      const {width, height} = this.$refs.loadprogresser.getBoundingClientRect()
      this.scales.w = width / this.maxSamples
      this.wrapHeight = height
      console.log(this.scales.w, width, this.maxSamples)
      this.transform = `scale( 1, -1) translate(0,${-this.wrapHeight}) rotate(0)`
    },
    onResize(){

    },
    getVScale(){
      this.scales.h = (this.maxY == 0)? 0 : this.wrapHeight / this.maxY
    },
    //передаю значения выделяю максимум и выдаю шкалу относительно этого максимума
    getYMax({value = 0}){
      if (value > this.maxY) this.maxY = value
    },
    addTime({time = 0}){
      if (this.Samples < this.maxSamples) {
        this.getYMax({value:time})
        this.getVScale()
        this.timePoints.push(time) //интересуют Int
        this.Samples ++;
        this.TextPrc = `${((this.Samples * 100)/this.maxSamples) | 0} %`
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
      path +=`L ${this.timePoints.length * this.scales.w} 0 `// Z` контур можно не замыкать
      return path
    },
    updateStar(){
      this.addTime({time:(this.TimeM*Math.random() | 0)})
      this.TimeM *= 1.01
      this.d = this.getSVGTimePoints()
      setTimeout(this.updateStar, 1)
    }
  },
  computed:{

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

#sample path {
  opacity: 0.5;
	fill: lightgreen;
	stroke: green;
	stroke-width: 1;
	transition: all 350ms;
}

#sample text {
  opacity: 0.5;
} 

.wrapper {
  position: relative;
  font-size: 2em;
  /*border: 1px red solid;*/
  width: 200px;
  height: 150px;
  line-height: 150px;
  overflow: hidden;
  text-align: center;
}

.wrapper h2 {
  opacity: 0.6;
  left: 0px;
  top: 0px;
  width: 100%;
  margin: 0 auto;
  position: absolute;
}

.star {
  stroke: #005000;
  stroke-width: 4;
  fill: none;
}

</style>
