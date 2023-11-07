<template>
    <div>
      <button @click="onchat">click</button>
      <textarea rows="10" cols="50" v-model="consoleOutput"></textarea>
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref } from 'vue';
  
  const consoleOutput = ref('');
  
  const captureConsoleLog = (message: string) => {
    consoleOutput.value += message + '\n';
  };
  
  const onchat = () => {
    consoleOutput.value = ''; // 清空先前的输出
    var myHeaders = new Headers();
    myHeaders.append("Authorization", "Bearer fk204878-8B2CVJYFsTZkxlwk20qCKyAsyr7KwVem");
    myHeaders.append("User-Agent", "Apifox/1.0.0 (https://apifox.com)");
    myHeaders.append("Content-Type", "application/json");
  
    var raw = JSON.stringify({
      "model": "gpt-3.5-turbo",
      "messages": [
        {
          "role": "user",
          "content": "讲个笑话"
        }
      ],
      "safe_mode": false
    });
  
    var requestOptions = {
      method: 'POST',
      headers: myHeaders,
      body: raw,
      redirect: 'follow' as const
    };
  
    fetch("https://oa.api2d.net/v1/chat/completions", requestOptions)
      .then(response => response.text())
      .then(result => {
        captureConsoleLog(result); // 捕获控制台输出
        // 使用$nextTick等待页面更新，然后滚动到底部以显示最新输出·
        consoleOutput.value += result;
        setTimeout(() => {
          const textarea = document.querySelector('textarea');
          if (textarea) {
            textarea.scrollTop = textarea.scrollHeight;
          }
        }, 0);
      })
      .catch(error => captureConsoleLog('error ' + error));
  }
  </script>
  