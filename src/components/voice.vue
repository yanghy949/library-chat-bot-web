<template>
  <div>
    <button class="voice" @click="toggleRecording" style="background-color: unset;border-radius:25%">
      <!--      {{ isRecording ? '停止录音' : '开始录音' }}-->
      <svg viewBox="0 0 1024 1024" width="25px" height="100%" v-if="!isRecording">
        <path
          d="M511.752 70.5c-86.605 0-156.835 69.734-156.835 155.747l0 273.812c0 86.013 70.23 155.748 156.835 155.748 86.602 0 156.832-69.735 156.832-155.748L668.584 226.247C668.584 140.234 598.354 70.5 511.752 70.5L511.752 70.5 511.752 70.5zM243.854 461.102c-18.051 0-32.649 14.496-32.649 32.451 0 2.269 0.197 4.436 0.689 6.506l-0.689 0c0 151.605 113.922 276.578 261.386 295.713l0 80.687-52.275 0c-21.702 0-39.257 17.458-39.257 38.964 0 21.499 17.555 38.957 39.257 38.957l182.969 0c21.701 0 39.256-17.458 39.256-38.957 0-21.506-17.555-38.964-39.256-38.964L551.01 876.459l0-80.687c143.119-18.543 254.383-137.002 260.691-282.688 0.396-2.072 0.695-4.243 0.695-6.512 0-0.79-0.197-1.479-0.197-2.167 0-1.483 0.197-2.86 0.197-4.345l-0.695 0c-3.058-14.795-16.172-25.94-32.057-25.94-15.782 0-28.999 11.145-32.057 25.94l-0.688 0c0 129.019-105.344 233.572-235.249 233.572-129.903 0-235.249-104.554-235.249-233.572l-0.689 0c0.396-2.07 0.689-4.237 0.689-6.506C276.503 475.598 261.906 461.102 243.854 461.102L243.854 461.102 243.854 461.102zM243.854 461.102"
          fill="#FFFFFFFF"></path>
      </svg>
      <svg viewBox="0 0 1024 1024" width="25px" height="100%" v-if="isRecording">
        <path
          d="M841.927111 621.283556a40.96 40.96 0 0 1 11.377778 56.803555c-80.156444 120.177778-180.963556 187.960889-300.344889 200.448v63.544889h122.88a40.96 40.96 0 0 1 4.778667 81.635556l-4.778667 0.284444H348.16a40.96 40.96 0 0 1-4.778667-81.635556l4.778667-0.284444h122.88v-63.544889c-119.352889-12.458667-220.16-80.241778-300.316444-200.448a40.96 40.96 0 0 1 68.152888-45.454222c74.695111 112.071111 164.750222 166.087111 273.123556 166.087111 108.373333 0 198.428444-54.044444 273.123556-166.087111a40.96 40.96 0 0 1 56.803555-11.377778zM512 0a163.84 163.84 0 0 1 163.84 163.84v409.6a163.84 163.84 0 1 1-327.68 0V163.84A163.84 163.84 0 0 1 512 0zM184.32 204.8a40.96 40.96 0 0 1 40.675556 36.181333l0.284444 4.778667v245.76a40.96 40.96 0 0 1-81.635556 4.778667l-0.284444-4.778667V245.76c0-22.613333 18.346667-40.96 40.96-40.96z m655.36 0a40.96 40.96 0 0 1 40.675556 36.181333l0.284444 4.778667v245.76a40.96 40.96 0 0 1-81.635556 4.778667l-0.284444-4.778667V245.76c0-22.613333 18.346667-40.96 40.96-40.96zM40.96 245.76a40.96 40.96 0 0 1 40.675556 36.181333l0.284444 4.778667v163.84a40.96 40.96 0 0 1-81.635556 4.778667L0 450.56v-163.84c0-22.613333 18.346667-40.96 40.96-40.96z m942.08 0a40.96 40.96 0 0 1 40.675556 36.181333l0.284444 4.778667v163.84a40.96 40.96 0 0 1-81.635556 4.778667l-0.284444-4.778667v-163.84c0-22.613333 18.346667-40.96 40.96-40.96z"
          fill="#d81e06"></path>
      </svg>
    </button>
  </div>
</template>

<script>
import axios from "axios";
// const URL = "https://10.1.40.110:8000/voice"
// const URL = "http://localhost:8000/voice"
const URL="https://"+window.location.hostname+":8000/voice"
// const  URL ="https://41a2-221-238-213-133.ngrok-free.app/vouice"
export default {
    // eslint-disable-next-line vue/multi-word-component-names
    name:"voice",
  data() {
    return {
      recorder: null,
      chunks: [],
      isRecording: false, // 新增的状态变量
    };
  },
  methods: {
    async toggleRecording() {
      if (this.isRecording) {
        try {
          this.recorder.stop();
          // 添加 onstop 事件监听器
          this.recorder.onstop = async () => {
            const blob = new Blob(this.chunks, {'type': 'video/webm; codecs=vorbis'});
            const formData = new FormData();
            formData.append('file', blob);
            const res = await axios.post(URL, formData, {headers: {'Content-Type': 'multipart/form-data'}});
            // 处理响应或执行其他操作
            this.$emit('voiced',res.data.text)
          }
        } catch (error) {
          console.error('Error while stopping the recorder:', error);
        } finally {
          this.isRecording = false; // 停止录音后，将状态切换为非录音状态
          this.chunks = []; // 清空录音片段数组，为下一次录音做准备
        }
      } else {
        try {
          const stream = await navigator.mediaDevices.getUserMedia({audio: true});
          this.recorder = new MediaRecorder(stream);
          this.recorder.ondataavailable = event => {
            this.chunks.push(event.data);
          };
          this.recorder.start();
        } catch (error) {
          console.error('Error while starting the recorder:', error);
        } finally {
          this.isRecording = true; // 开始录音后，将状态切换为录音状态
        }
      }
    },
  },
};
</script>
<style>
.voice {
  position: relative;
  border-radius: 25%;
  background-color: unset;
  left: -6vh;
  height: 50%;
  width: auto;

}
.voice:hover {
  border-color: lightslategray;
}

.voice:active {
  border-color: lightslategray;
}
</style>
