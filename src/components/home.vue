<template>
  <div class="home">
    <div style="all: initial" height="100%">
      <div style="all: initial" width="100px">
        <Nav @msg="sendmsg"></Nav>
        <chat :msg="msg"></chat>
      </div>
    </div>

  </div>
</template>

<script>
import Nav from './Nav.vue'
import chat from "./chat.vue";
export default {
    // eslint-disable-next-line vue/multi-word-component-names
  name: "home",
  components:{
    Nav,
    chat,
  },
  data(){
    return{
      msg:''
    }
  },
  methods:{
    sendmsg(event) {
      this.msg=event
    }
  }
}
</script>

<style scoped>
.home{
  width: 90vw;
  height: 90vh;
  background-color: rgb(39,42,55);
  border-radius: 15px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform:translate(-50%,-50%);
}
</style>
