<template>
  <div ref="node" class="node" :style="{left:x+'px',top:y+'px', borderColor: borderColor}" 
  @mousedown="activate" 
  @mousemove="move" 
  @mouseup="deactivate"
  @mouseleave="deactivate">
    <p class="label">{{val}}</p>
  </div>

  <svg v-if="left!==null" class="line">
    <!-- <line :x1="x+25" :y1="y+25" :x2="leftpos.x" :y2="leftpos.y" style="stroke:green;stroke-width:2"/> -->
    <line :x1="x+25" :y1="y+25" :x2="leftpos.x" :y2="leftpos.y" stroke="black" stroke-width="2"/>
  </svg>
  <svg v-if="right!==null" class="line"> 
    <line :x1="x+25" :y1="y+25" :x2="rightpos.x" :y2="rightpos.y" stroke="black" stroke-width="2"/>
  </svg>

</template>

<script>

export default {
  computed:{
    leftpos(){
      if (this.left!==null)
      {
        return {x: this.get_nodeBy_index(this.left).x+25, y: this.get_nodeBy_index(this.left).y+25};
      }
      else{return {x: null,y: null};}
    },
    rightpos(){
      if (this.right!==null)
      {
        return {x: this.get_nodeBy_index(this.right).x+25, y: this.get_nodeBy_index(this.right).y+25};
      }
      else{return {x:null,y: null};}
    },
    objectElement(){
      return this.nodes_list[this.node];
    },
  },
  data(){
    return{
      ...this.nodes_list[this.node],
      dragging: false,
      borderColor: '08ff88',
    };
  },
  props:{
    node: Number,
    last_active: Number
  },
  inject:{
    nodes_list: "nodes_list",
    pairing_mode: "pairing_mode",
    get_nodeBy_index: "get_nodeBy_index",
    node_status_setter: "node_status_setter",
    update_xy: "update_xy",
  },
  methods:{
    activate(){
      this.dragging = true;
      this.node_status_setter(this.index);
    },
    move(e){
      if (this.dragging){
        let x = e.clientX - 25;
        let y =  e.clientY - 25;
        this.x = x;
        this.y = y;    
        this.update_xy(this.index, x, y); 
      }
    },
    deactivate(){
      if (this.dragging){
        this.node_status_setter(null);
      }
      this.dragging = false;

    },

  },
  watch:{
    objectElement:
    {
      handler(newVal){
        // console.log("detected change");
        this.val = newVal.val;
        this.left = newVal.left;
        this.right = newVal.right;
        this.index = newVal.index;
        this.x = newVal.x;
        this.y = newVal.y;
        this.depth = newVal.depth;
      },
      deep: true
    },
    last_active:{
      handler(newVal){
        if (newVal === this.index)
        {
          this.borderColor= 'red';
        }
        else{
          this.borderColor= '#08ff88';
        }
      },
    }
  },
  mounted(){
    console.log("mounted!",this.node);
  },
  provide(){
    return{
      get_nodeBy_index: this.get_nodeBy_index,
      node_status_setter: this.node_status_setter,
      update_xy: this.update_xy,
    }
  },

};
</script>

<style scoped>
.line{
  position: absolute;
  pointer-events: none;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.node{
  position: absolute;
  text-align: center;
  height: 50px;
  width: 50px;
  border-radius: 50%;
  box-sizing: border-box;
  background-color: lightgray;
  color: #000000;
  border: 5px solid #08ff88;
  cursor: pointer;
  user-select: none;
  box-shadow: 1px 1px 5px 1px #888;
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center;
  z-index: 1000;
}
.node:hover{
  background-color: #00ff00;
}
</style>
