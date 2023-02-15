<template>
  <div class="chat-container">
    <p class="title">chat-gpt</p>
    <div class="chat-content" ref="chatContent">
      <div class="chat-list" v-for="(item, index) of list" :key="index">
        <div class="chat-item" :style="{ flexDirection: item.type ? 'row-reverse' : 'row' }">
          <div class="avatar">{{ item.type ? "me" : "gpt" }}</div>
          <div class="message-content" v-html="item.message"></div>
        </div>
      </div>
    </div>
    <div class="enter-box">
      <textarea class="input-box" v-model="message" @keyup.enter="submit"></textarea>
      <button class="enter-btn" @click="submit">发送</button>
    </div>
  </div>
</template>
    
<script setup>
import axios from "axios";
import { reactive, ref } from "@vue/reactivity";
import { nextTick, watch } from "vue";
const message = ref("");
const list = reactive([]);
const chatContent = ref(null)

watch(list, () => {
  nextTick(() => {
    let content = chatContent.value;
    content.scrollTop = content.scrollHeight
  })
})

function submit() {
  if (!message.value) return;
  console.log(message.value);
  let msg = message.value;
  list.push({
    type: 1,
    message: msg,
  });
  message.value = "";
  axios.get(`http://localhost:8999/askbasic?question=${msg}`).then((res) => {
    console.log(res.data);
    list.push({
      type: 0,
      message: res.data,
    });
  });
}

</script>
    
<style lang="less" scoped>
.chat-container {
  width: 375px;
  height: 100vh;
  border: 1px solid #ccc;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  background: #f4f5f7;

  .title {
    height: 32px;
    text-align: center;
    line-height: 32px;
    font-size: 16px;
    font-weight: 600;
    border-bottom: 1px solid #ccc;
  }

  .chat-content {
    flex: 1;
    overflow: auto;

    &::-webkit-scrollbar {
      width: 10px !important;
      /*高宽分别对应横竖滚动条的尺寸*/
      height: 1px !important;
    }

    &::-webkit-scrollbar-thumb {
      /*滚动条里面小方块*/
      border-radius: 10px !important;
      background-color: rgb(201, 201, 201) !important;
      /*background-color: 0096c7 !important;*/
      background-image: -webkit-linear-gradient(45deg,
          rgba(255, 255, 255, 0.2) 25%,
          transparent 25%,
          transparent 50%,
          rgba(255, 255, 255, 0.2) 50%,
          rgba(255, 255, 255, 0.2) 75%,
          transparent 75%,
          transparent);
    }

    &::-webkit-scrollbar-track {
      /*滚动条里面轨道*/
      box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
      background: #ededed;
      border-radius: 10px;
    }


    .chat-item {
      margin: 16px 12px 0 12px;
      display: flex;
      justify-content: flex-start;

      .avatar {
        width: 40px;
        height: 40px;
        border-radius: 50%;
        background: rgb(161, 161, 161);
        color: #fff;
        text-align: center;
        line-height: 40px;
      }

      .message-content {
        line-height: 1.5;
        font-size: 14px;
        padding: 8px 16px;
        word-wrap: break-word;
        word-break: break-word;
        border-radius: 0 16px 16px 16px;
        overflow: hidden;
        background: #fff;
        flex: 1;
        margin: 0 8px;
      }
    }
  }

  .enter-box {
    border-top: 1px solid #ccc;
    height: 120px;
    position: relative;

    .input-box {
      width: 100%;
      height: 100%;
      border: 0;
      resize: none;
      box-sizing: border-box;
      padding: 8px;
      font-size: 14px;
      font-weight: 100;
    }

    .enter-btn {
      width: 108px;
      height: 32px;
      border: 1px solid #eee;
      background: #fff;
      border-radius: 5px;
      position: absolute;
      right: 8px;
      bottom: 8px;
      cursor: pointer;

      &:hover {
        background: rgb(5, 211, 84);
        color: #fff;
        border: 0;
      }
    }
  }
}
</style>