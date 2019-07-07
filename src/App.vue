//Если требуется реагировать на каждую передачу в props то надо передавать объект
<template>
  <div>
    <h2>Progresser</h2>
    <progresser
      class="progresser"
      :maxSamples = "SamplesInProgresser"
      :Point = "Point"
    ></progresser>          
  </div>
</template>

<script>
import Progresser from "./components/Progresser"
export default {
  name: 'app',
  data(){
    return {
      SamplesInProgresser:400,
      TimeM:100,
      Point:{value:0},
      index:0
    }
  },
  created: function () {
    this.updateStar()
  },
  methods:{
    updateStar(){
      if (this.index < this.SamplesInProgresser) {
        this.index++;
        this.Point = {value:(this.TimeM*Math.random() | 0)}
        this.TimeM *= 1.01
        setTimeout(this.updateStar, 1)
      }
    }    
  },

  components: {
    Progresser
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  margin-top: 60px;
}

.progresser {
  width: 300px;
  height: 80px; 
}
</style>
