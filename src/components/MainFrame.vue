<template>
  <div class="mainFrame">
    <div v-for="(value, key, index) in nodes_list" :key="index">
      <NodeS :node="parseInt(key)" :last_active="last_active"/>
    </div>
    <!-- <NodeS :node="nodeB" :pairing="pairing_mode" @parentFinding="handler"/> -->
    <svg v-if="pairing_mode && active" class="line"> 
      <line :x1="current_active_x+25" :y1="current_active_y+25" :x2="last_active_x+25" :y2="last_active_y+25" stroke="green" stroke-width="2" stroke-dasharray="5 5"/>
    </svg>
  </div>
</template>

<script scoped>
import NodeS from "./NodeS.vue";
export default {
  name: 'MainFrame',
  components: {
    NodeS
  },
  computed:{
    last_active_x(){
      if (this.last_active !== null){
        return this.nodes_list[this.last_active].x;
      }
      else{
        return 0;
      }
    },
    last_active_y(){
      if (this.last_active !== null){
        return this.nodes_list[this.last_active].y;
      }
      else{
        return 0;
      }
    },
    current_active_x(){
      if (this.active !== null){
        return this.nodes_list[this.active].x;
      }
      else{
        return 0;
      }
    },
    current_active_y(){
      if (this.active !== null){
        return this.nodes_list[this.active].y;
      }
      else{
        return 0;
      }
    },
  },
  data(){
    return{
      index:3,
      pairing_mode: false,
      nodes_list:{      
      0: {val:12, x: 100, y: 270, index: 0, left: null, right: null, depth: 0},
      1: {val:13, x: 50, y: 200, index: 1, left: 0, right: 2, depth: 0},
      2: {val:15, x: 80, y: 400, index: 2, left: null, right: null, depth: 0},
      },
      active: null,
      last_active:null,
      bc: "#88ffff",
    };
  },
  methods:{
    pairing_on(e){
      if (e.code === 'ControlLeft')
      {
        this.pairing_mode = true;
        console.log(this.last_active);
      }
    },
    pairing_off(e){
      if (e.code === 'ControlLeft')
      {
        this.pairing_mode = false;
      }
    },
    node_status_setter(status){
      if (this.active !== null){
        if(this.pairing_mode===true && this.last_active!==null && this.active != this.last_active)
        {
          let left = this.nodes_list[this.active].left;
          let right = this.nodes_list[this.active].right;
          if (left === null & right === null){
            this.insert();
          }
          else{
            console.log("current node is not leave");
          }
          console.log(this.nodes_list);
        }
        this.last_active=this.active;

      }

      this.active=status;
    },
    append(x,y,value){
      let node={val:value, x: x, y: y, index: this.index, left: null, right: null};
      this.nodes_list[this.index]=node;
      this.index+=1; 
    },
    change(value){
      this.nodes_list[this.index-1].val=value;
    },
    update_xy(index,x,y, increment=false){
      if (index !== null){
        if (increment === true){
          this.nodes_list[index].x+=x;
          this.nodes_list[index].y+=y;
        }
        else{

          let old_x = this.nodes_list[index].x;
          let old_y = this.nodes_list[index].y;
          this.nodes_list[index].x=x;
          this.nodes_list[index].y=y;
          x = x - old_x;
          y = y - old_y;
        }
        this.update_xy(this.nodes_list[index].left, x, y, true);
        this.update_xy(this.nodes_list[index].right, x, y, true);
      }
    },
    insert(){
      console.log("insert called");
      let x = this.nodes_list[this.active].x;
      let value = this.nodes_list[this.active].val;
      let target_x = this.nodes_list[this.last_active].x;
      let target_value = this.nodes_list[this.last_active].val;
      if (x <= target_x && value<=target_value && this.last_active.left==null){
        this.nodes_list[this.last_active].left = this.active;
        console.log("left insertion");
      }
      else if (x > target_x && value>target_value && this.last_active.right==null){
        this.nodes_list[this.last_active].right = this.active;
        console.log("right insertion");
      }
      else{
        console.log("Invalid insert");
      }
    },

    get_nodeBy_index(index){
      return (this.nodes_list[index]);
    },
    del_node(){
      if (this.last_active!==null && this.nodes_list[this.last_active].left===null && this.nodes_list[this.last_active].right===null){      
        let index=this.last_active.toString();
        for (let i in this.nodes_list)
        {
          if (this.nodes_list[i].left === this.last_active)
          {
            this.nodes_list[i].left = null;

          }
          if (this.nodes_list[i].right === this.last_active)
          {
            this.nodes_list[i].right = null;
          }
        }
        delete this.nodes_list[index];
      }
      else{console.log("invalid node to delete");}
    }
    
  },
  mounted(){
      document.addEventListener('keydown', this.pairing_on.bind(this));
      document.addEventListener('keyup', this.pairing_off.bind(this));
    },
  beforeUnmount() {
    document.removeEventListener('keydown', this.pairing_on.bind(this));
    document.removeEventListener('keyup', this.pairing_off.bind(this));
  },
  provide(){
    return{
      pairing_mode: this.pairing_mode,
      get_nodeBy_index: this.get_nodeBy_index,
      node_status_setter: this.node_status_setter,
      update_xy: this.update_xy,
      nodes_list: this.nodes_list,
  
    }
  },
  props:{
    stopGen: Function,
  },
  watch:{
    active(){
      if(this.active === this.index-1)
      {this.stopGen();}
    }
  }
}
</script>

<style scoped>
.mainFrame{
  background-color: 'red';
}
.line{
  position: absolute;
  pointer-events: none;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>
