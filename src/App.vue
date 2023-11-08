<template>
  <div id="zym">
    <div id="xiao">
    <button @click="onChat" class="hover:bg-red-600 focus:text-green-500 border-2 border-purple-600" mar>开始</button>
    <div id="td">
    <textarea id="hf" rows="10" cols="50" v-model="consoleOutput"></textarea>
  </div>
  </div>
  </div>
</template>
<link rel="stylesheet" href="F:\11.6\aichatvoice\src\style.css">




<script setup lang="ts">
import { ref } from 'vue';

 

const consoleOutput = ref('');

const onChat = async () => {
  consoleOutput.value = ''; // Clear previous output

  const myHeaders = new Headers();
  myHeaders.append("Authorization", "Bearer fk204878-8B2CVJYFsTZkxlwk20qCKyAsyr7KwVem");
  myHeaders.append("User-Agent", "YourApp/1.0.0");
  myHeaders.append("Content-Type", "application/json");

  const requestBody = JSON.stringify({
    "model": "gpt-3.5-turbo",
    "messages": [
      {
        "role": "user",
        "content": "我要怎么获取一个好用的端口"
      }
    ],
    "stream": true,
    "safe_mode": false
  });

  const requestOptions: RequestInit = {
    method: 'POST',
    headers: myHeaders,
    body: requestBody,
    redirect: 'follow'
  };

  try {
    const response = await fetch("https://oa.api2d.net/v1/chat/completions", requestOptions);

    if (response.body) {
      const reader = response.body.getReader();
      
      // Function to process each chunk
      const processChunk = async ({ done, value }: { done: boolean, value?: Uint8Array }) => {
        if (done) {
          // Stream is complete
          return;
        }
        // Decode the Uint8Array to a string
        const chunk = new TextDecoder().decode(value);
        try {
          // Parse the chunk as JSON          
          const json = JSON.parse(chunk.replace("data: ",""));
          // Extract the content from the choices array, if available
          const content = json.choices?.[0]?.delta?.content;
          if (content) {
            consoleOutput.value += content; // Append the content to the textarea value
          }
        } catch (error) {
          // Handle JSON parsing error or the structure not being what is expected
          console.error('Error parsing JSON from chunk:', error);
        }
        // Read the next chunk
        reader.read().then(processChunk);
      };

      // Start reading the stream
      reader.read().then(processChunk);
    } else {
      consoleOutput.value = "The response is not a stream.";
    }
  } catch (error) {
    consoleOutput.value = `Error: ${error}`;
  }
}
</script>
