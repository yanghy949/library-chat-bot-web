<template>
<div class="nav">
<p class="title" >图书馆</p>
<p class="title1">热门问题</p>
  <div class="menu" style="align-items: center;">
    <ul class="menu-list">
      <li v-for="item in menulist" :key="item" class="list" @click="sendmsg">
        <div class="box">
          <span class="ite" style="cursor: pointer;">{{item}}</span>
        </div>
      </li>
    </ul>
  </div>
</div>
</template>

<script>
export default {
    // eslint-disable-next-line vue/multi-word-component-names
  name: "Nav",
  data(){
    return{
      menulist:[
        '电脑自习室几点开放',
        '哪里可以用电脑',
        '如何找书',
        '你知道北京科技大学天津学院吗',
        '介绍一下北京科技大学天津学院',
        '图书馆的开放时间是什么时候？',
        '图书可以续借几次？',
        '周六可以还书吗？',
        '如何借书？',
        '如何还书？',
        '借还书的时间是什么时候？',
        '寒暑期可以借还书吗？',
        '如何续借图书？',
        '图书借阅几天归还？',
        '请问下图书馆一次可以借几本书呀？',
        '在图书馆可以连校园网吗？',
        '图书馆网站网址是什么？',
        '有电子版图书吗？/电子书如何借阅？',
      ],
    }
  },
  methods:{
    sendmsg(event) {
      const item = event.currentTarget.querySelector('.ite').textContent;
      this.$emit('msg',item)
      event.stopPropagation()
    },
  },


}
</script>

<style scoped>
.nav {
  width: 25%;
  height: 90vh;
  position: relative;
  border-radius: 20px 0 0 20px;
  display: flex;
  align-items: center;
  .menu {
    position: absolute;
    margin: 0 auto;
    top: 55%;
    width: 100%;
    height: 80%;
    transform: translate(0, -50%);
    overflow-y: scroll;
    .menu-list {
      margin: 0 auto;
    }
  }
  .menu::-webkit-scrollbar{
    width: 0;
    height: 0;
  }
  .list{
    margin-top: 5px;
    width: 90%;
    height: 4vh;
    display: flex;
    align-items: center;
    .box{
      width: 100%;
      height: 90%;
      background-color: rgb(64,67,80);
      display: flex;
      align-items: center;
      border-radius: 20px 20px 20px 20px;
      overflow: hidden;

      .ite{
        color: rgb(150,150,150);
        display: inline-block;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        -webkit-user-select: none;
      }
      &:hover .ite{
        animation: scroll 5s linear infinite;
        text-overflow: unset;
        overflow: initial;
        color: white;
      }
      &:hover{
        background-color: deepskyblue;
      }
    }
    .box span{
      margin-left: 10px
    }
  }
}
.title{
  position: absolute;
  top: 1vh;
  left: 3vh;
  font-size: 3vh;
  color: whitesmoke
}
.title1{
  position: absolute;
  top: 8vh;
  left: 6vh;
  font-size: 2vh;
  color: rgb(200,200,200);
}
@keyframes scroll {
  0% {
    transform: translateX(10%);
  }
  100% {
    transform: translateX(-50%);
  }
}

</style>
