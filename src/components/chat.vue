<template>
  <div class="chat">
    <state></state>
    <div class="chatwindow">
      <div class="frame">
        <div class="msgbox" ref="msgBox">
          <div v-for="[key,msg,date] in messages" :key="key" class="msg-box">
            <div class="msg-box1" v-if="key==='get'">
              <div class="msg1">{{ msg }}</div>
            </div>
            <div class="msg-box2" v-if="key==='send'">
              <div class="msg2">{{ msg }}</div>
            </div>
            <div class="date" :id=key v-if="key==='get'">
              <Head></Head>
              <p style="all: initial;font-weight: bold;color: white"> 小贝 </p>{{ date }}
            </div>
            <div class="date" :id=key v-if="key==='send'">{{ date }}
              <p style="all: initial;font-weight: bold;color: white"> 小贝壳 </p>
              <Head :custom-style="{backgroundColor:'cadetblue'}"></Head>
            </div>
          </div>

          <!--          参考文档-->
<!--          <h3 align="center" v-if="!showCont" @click="setcont" class="ckwd">>>参考文档<<</h3>-->
<!--            <div style="display: flex;justify-content: center;align-items:center;margin-bottom:20px;margin-top:20px">-->
<!--            <div style="background: rgb(151,157,167);border-radius: 15px;width: 80%" v-if="cont.length">-->
<!--              <h3 align="center" v-if="showCont" style="user-select: none;" @click="setcont">&#8595;参考文档&#8595;</h3>-->
<!--              <div class="word" v-if="showCont">-->
<!--                <div v-html="cont"></div>-->
<!--              </div>-->
<!--            </div>-->
<!--          </div>-->

        </div>
        <div style="height: 25px"></div>

        <p class="warn" id="war" v-show=false><i class="el-icon-error"></i>不能发送空内容!</p>
        <!--        <p class="warn" id="voice" v-show=false>正在录音</p>-->

        <div class="input-box">
          <input type="text" class="input" id="input" autocomplete="off" v-model="inputtext" @keyup.enter="send"
                 @input="msgtext"
                 autofocus placeholder="在这里输入内容或使用语音录入"/>

          <voice title="使用语言输入" @voiced="voiced"></voice>
          <button class="send" @click="send" :disabled="this.inputtext.length===0">
            <svg class="icon" viewBox="0 0 1024 1024" width="100%" height="100%">
              <path
                d="M955 125.6c-4.5-12.5-15.4-21.8-28.6-24.2-9.2-1.7-18.4 0.1-26.1 4.8L83.9 544.3c-12.8 6.8-20.6 20.6-19.8 35.1 0.8 14.3 9.9 27.3 23.2 32.9l238.5 97.9c12.4 5.2 26.8 3.4 37.5-4.9s16.1-21.3 14.4-34.8c-1.6-13.2-10.4-24.6-23-29.9l-165-67.7 573-307.3-357 421.9c-6.5 7.7-9.6 17.4-8.8 27.4L411.4 884c1.6 19.7 17.7 34.6 37.5 34.6 9.5 0 18.7-3.7 25.8-10.4l74-69.2 0.1-0.1c13.5-13.2 15.2-33.5 4.8-48.4l222.6 72c4 1.3 7.9 2 11.7 2 18.2 0 33.8-12.9 37-30.6l131.6-688.3h-0.1c1.4-6.6 0.9-13.5-1.4-20zM497.3 783.8L479.9 800l-6.5-75.9L856 271.6l-96.8 506.3L539 706.6c-19.7-6.4-41.1 4.4-47.5 24.2-5.8 17.9 2.5 37.1 18.9 45.4-4.7 1.5-9.2 4-13.1 7.6z"></path>
            </svg>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import state from "./state.vue";
import Head from "./Head.vue";
import axios from "axios";
// import markdownIt from 'markdown-it';
import voice from "./voice.vue";
// import {data} from "autoprefixer";


const URL = "https://"+window.location.hostname+":8000"
// const URL="https://10.1.40.110:8000"
///
export default {
    // eslint-disable-next-line vue/multi-word-component-names
  name: "chat",
  props: ['msg'],
  components: {
    state,
    Head,
    voice
  },
  data() {
    return {
      showCont: 'false',
      // voicewar:false,
      cont: '',
      inputtext: '',
      messages: [],
      text: '',
      message: '',
      transcription: '',
      history: [
        ["从现在开始你是贝壳图书馆的聊天机器人，我会称呼你为小贝", "好的，我是贝壳图书馆的聊天机器人，你可以叫我小贝"]
      ],
    }
  },

  methods: {
    voiced(message) {
      console.log(message);
      // this.inputtext = message
      this.poster(message)
    },
    diytime(date) {
      const year = date.getFullYear();
      const month = date.getMonth() + 1;
      const day = date.getDate();
      const hour = date.getHours();
      const minute = date.getMinutes();
      const second = date.getSeconds();
      // 使用模板字符串拼接日期字符串
      return `${year}-${month}-${day} ${hour}:${minute}:${second}`;
    },

    async poster(msg) {
      const now = new Date()
      this.messages.push(['send', msg, this.diytime(now)])
      const res = await axios.post(URL, {
        prompt: msg,
        history: this.history,
        temperature: 0.5,
        max_length: 2048,
        top_p: 0.7
      })
      this.messages.push(["get", res.data.response, res.data.time])
      this.history = res.data.history
      // this.cont = markdownIt().render(res.data.cont)
      localStorage.setItem('savedData', JSON.stringify(res.data));
    },

    setcont() {
      this.showCont = !this.showCont; // 点击时切换showCont的值
    },

    send() {
      const text = this.inputtext.trim()
      if (text.length !== 0) {
        this.poster(text)
      } else {
        // 获取要操作的元素
        const war = document.getElementById('war');
        war.style.display = 'block';
        setTimeout(function () {
          war.style.display = 'none';
        }, 800);
      }
      this.inputtext = ''
    },
    msgtext() {
      console.log(this.inputtext);
    },


  },
  watch: {
    messages() {
      this.$nextTick(() => {
        const messageBox = this.$refs.msgBox;
        messageBox.scrollTop = messageBox.scrollHeight;
        // 将滚动条滚动到结尾处
      });
    },
    msg() {
      this.poster(this.msg)
    }
  },
  created() {
    const now = new Date()
    this.messages.push(['get', '我是图书馆小贝，有任何问题欢迎提问！', this.diytime(now)]);
    this.showCont = "false"
  },
};

</script>

<style scoped>

.chat {
  float: right;
  top: -100%;
  width: 75%;
  height: 100%;
  position: relative;
  border-radius: 0 20px 20px 0;
}

.chatwindow {
  position: relative;
  top: 2vh;
  width: 65vw;
  height: 72vh;
  border-radius: 20px 20px 20px 20px;
  background-color: rgb(64, 67, 80);
}

.frame {
  width: 100%;
  height: 90%;
  border-radius: 20px 20px 20px 20px;
  overflow: auto;
}

.frame::-webkit-scrollbar {
  width: 0;
  height: 0;
}

.input-box {
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 10%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.input {
  background-color: rgb(59, 62, 75);
  color: rgb(200, 200, 200);
  font-size: 23px;
  width: 70%;
  height: 70%;
  border-radius: 15px;
  border: solid lightskyblue;
  overflow: hidden;
  padding-right: 7vh;
  word-wrap: break-word;

  &:focus {
    outline: 1px solid skyblue;
  }
}

.voice {
  position: relative;
  border-radius: 25%;
  background-color: unset;
  left: -6vh;
  height: 50%;
  width: auto;

  &:hover {
    border-color: lightslategray;
  }
}

.voice:active {
  border-color: lightslategray;
}

.send {
  background-color: lightskyblue;
  position: relative;
  left: -2vh;
  height: 70%;
  width: 6%;
  border-radius: 10px;

  &:hover:after {
    content: "点击发送";
    background-color: white;
    white-space: nowrap;
    color: black;
    border: 2px;
    padding: 8%;
    position: absolute;
    bottom: -80%;
    left: 50%;
    transform: translateX(-50%);
    border-radius: 10px;
  }
}

.send:disabled {
  background-color: rgb(90, 161, 205);
  cursor: not-allowed;

  &:hover::after {
    content: "不能发送空内容";
    background-color: white;
    white-space: nowrap;
    color: black;
    border: 2px;
    padding: 8%;
    position: absolute;
    bottom: -80%;
    left: 50%;
    transform: translateX(-50%);
    border-radius: 10px;
  }
}

.warn {
  background: rgba(255, 255, 255, 0.8);
  /*background: #f8d7da;  */
  font-size: 2vh;
  border: 1px solid rgba(255, 255, 255, 0.5);
  border-radius: 10px;
  padding: 1%;
  color: red;
  position: absolute;
  right: 30vw;
  bottom: 9vh;
  z-index: 9999;
}

.msgbox {
  width: 100%;
  height: 100%;
  overflow: auto;
  position: relative;
  padding-bottom: 10vh;
  border-radius: 15px;
}

.msgbox::-webkit-scrollbar {
  width: 0;
  height: 0;
}

.msg-box {
  width: 100%;
  position: relative;
  padding-bottom: 1vh;
}

.msg-box1 {
  display: flex;
  justify-content: start;
  padding-left: 2vh;
}

.msg-box2 {
  display: flex;
  justify-content: flex-end;
  padding-right: 2vh;
}

.msg1, .msg2 {
  background-color: lightskyblue;
  padding: 2vh;
  font-weight: bold;
  margin: 2vh;
  display: inline-block;
  max-width: 40vw;
  position: relative;
  word-wrap: break-word;
}

.msg1 {
  left: 0;
  border-radius: 20px 20px 20px 0;
}

.msg2 {
  right: 0;
  border-radius: 20px 20px 0 20px;
}

.date {
  position: absolute;
  bottom: -1vh;
  width: 45vh;
  color: rgb(150, 150, 150);
}

.word {
  height: 10vh;
  padding-top: 20vh;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: auto;
  scrollbar-width: none;
  &::-webkit-scrollbar{
    display: none;
  }
}
.ckwd{
  user-select: none;
  color: white;
  background-color: rgb(151,157,167);
  border-radius: 90vw;
  margin-left: 40%;
  margin-right: 40%;
}
#send {
  right: -18vh;
  padding-right: 0;
}

#get {
  left: 2vh;
}

</style>
