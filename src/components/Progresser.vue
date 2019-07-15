<template>
  <div class="wrapper" ref="loadprogresser">
    <svg class="wrapper__content" version="1.1" xmlns="http://www.w3.org/2000/svg"
          width="100%" height="100%">
        <g :transform="transform">
          <path :d="getPath"/>
        </g>
        <text x="50%" y="50%" dominant-baseline="central" text-anchor="middle">{{TextPrc}}</text>
    </svg>
  </div>
</template>

<script>
export default {
  props:{
    maxSamples: {//кол-во сэмплов в 100%-ах ширины
      type: Number,
      default: 400
    },
    Point:{
      value:0
    }
  },
  data(){
    return {
      Samples:0,//номер текущего сэмпла
      wrapHeight:100,//высота в которую надо вписать изображение
      maxY:0,//максимальная по Y величина (для вертикального масштабирования)
      scales:{
        w:1,//масштабирование по горизонтали (расчитывается по ширине контейнера)
        h:1 //масштабирование по вертикали
            //(пересчитывается по максимальному Y и высоте контейнера)
      },
      Points:[],//массив значений времени выполения операции (получены из Point.value)
      transform:'scale( 1, -1) translate(0,0)',
      TextPrc: '0%'
    }

  },
  mounted: function(){
    this.getScales()
  },
  methods:{
    getScales(){
      const {width, height} = this.$refs.loadprogresser.getBoundingClientRect()
      //Коэф. Масштабирования по горизонтали
      this.scales.w = width / this.maxSamples
      //трансформация координат Y от высоты контейнера
      this.wrapHeight = height
      this.transform = `scale( 1, -1) translate(0,${-this.wrapHeight}) rotate(0)`
    },
    getVScale(){//расчёт масштаба по вертикали
      this.scales.h = (this.maxY == 0)? 0 : this.wrapHeight / this.maxY
    },
    //передаю значения выделяю максимум и выдаю шкалу относительно этого максимума
    getYMax({value = 0}){
      this.maxY = (value > this.maxY)
        ? value
        : this.maxY 
    },
    addValue({value = 0}){
      if (this.Samples < this.maxSamples) {
        this.getYMax({value})
        this.getVScale()
        this.Points.push(value) //интересуют Int
        this.Samples ++;
        this.TextPrc = `${((this.Samples * 100)/this.maxSamples) | 0} %`
      }
    },
    getSVGPoints(){
      //теперь создаю строку для Path
      let predY = 0
      let path = this.Points.reduce((str, item)=>{
        let dY = (item - predY) * this.scales.h
        predY = item
        return str + `l ${this.scales.w} ${dY} `
      },'M 0 0 ')
      path +=`L ${this.Points.length * this.scales.w} 0 `// Z` контур можно не замыкать
      return path
    },
  },
  computed:{
    getPath(){
      this.addValue({value:this.Point.value})
      return this.getSVGPoints()//this.d
    }
  }
}
</script>

<style scoped>

.wrapper {
  width: 400px;/* размеры задаются в стилях прложения*/
  height: 100px;/*встроенные стили (поумолчанию) перекрываются стилями приложения*/
  font-size: 4rem;
  font-weight: 600;
  border-left: 1px gray solid;
  border-right: 1px gray solid;
  overflow: hidden;
}

.wrapper__content path {
  opacity: 0.5;
	fill: lightgreen;
	stroke: green;
	stroke-width: 1;
}

.wrapper__content text {
  opacity: 0.5;
} 
</style>
