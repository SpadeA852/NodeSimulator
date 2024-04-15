<template>
  <div class="subFrame">
    <MainFrame ref="mainFrame" class="mainFrame" :stopGen="stopGen"/>
    <div class="controlPanel">
      <h3>控制面板</h3>
      <div id="outlet">
        <div ref="ball" :style="{borderColor: color}" id="ball">{{ display }}</div>
      </div>
      <input type="text" id="inputA" placeholder="输入值" v-model.number="value" @keydown.enter="gen">
      <button id="delNode" @mouseup="$refs.mainFrame.del_node()">Delete Node</button>
    </div>
  </div>
</template>

<script scoped>
import MainFrame from './MainFrame.vue'
export default {
  name: 'controlPanel',
  components: {
    MainFrame
  },
  data(){
    return{
      value: "",
      display: "?",
      color:"888888",
      duringGen: false,
    }
  },
  methods:{
    gen(){
      if(Number.isInteger(this.value) && this.value < 100 && this.value > -100)
      {
        if(this.duringGen){
          this.$refs.mainFrame.change(this.value);
        }
        else{
          this.display = this.value;
          let rect = this.$refs.ball.getBoundingClientRect();
          this.$refs.mainFrame.append(rect.left, rect.top, this.value);
          this.duringGen= true;
        }
      }
      else{
        console.log(this.value, this.value.type, "输入错误");
      }
    },
    stopGen(){
      this.duringGen = false;
      this.display="?";
      this.value = "";
    }
  },
}
</script>

<style scoped>
.subFrame{
  background-color: #f0f0f0;
  width: 100%;
  height: 500px;

  border-radius: 20px;
  border: 5px dashed #f080f0;
  box-sizing: border-box;
  margin-left: 10px;
  display: flex;
}
#outlet{
  height: 20%;
  position: relative;
}
#ball{
  top: 10%;
  left: 50%;
  transform: translate(-25px,0);
  position: relative;
  text-align: center;
  height: 50px;
  width: 50px;
  border-radius: 50%;
  box-sizing: border-box;
  background-color: lightgray;
  color: #000000;
  border: 5px solid #888888;
  cursor: pointer;
  user-select: none;
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center;
}

#inputA{
  margin: 5px;
  text-align: center;
}
.container{
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.mainFrame{
  margin-right: auto;
  border-radius: 15px;
  background-color: #ffffff;
  width: 100%;
  border-radius: 20px;
  overflow: hidden;
}
.controlPanel{
  margin-left: auto;
  border-radius: 15px;
  background-color: #ffff88;
  margin: 10px;
}
#delNode{
  margin: 10px;
}
</style>
