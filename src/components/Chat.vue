<template>
  <div class="chat-container">
    <p class="title">chat-gpt</p>
    <div class="chat-content">
      <div class="chat-list" v-for="(item, index) of list" :key="index">
        <div
          class="chat-item"
          :style="{ flexDirection: item.type ? 'row-reverse' : 'row' }"
        >
          <div class="avatar">{{ item.type ? "me" : "gpt" }}</div>
          <div class="message-content">{{ item.message }}</div>
        </div>
      </div>
    </div>
    <div class="enter-box">
      <textarea
        class="input-box"
        v-model="message"
        @keyup.enter="submit"
      ></textarea>
      <button class="enter-btn" @click="submit">发送</button>
    </div>
  </div>
</template>
    
<script setup>
import axios from "axios";
import { reactive, ref } from "@vue/reactivity";
const message = ref("");
const list = reactive([]);

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