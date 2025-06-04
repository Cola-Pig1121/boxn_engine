<template>
  <div v-if="visible" class="dialog-overlay">
    <div class="dialog-box">
      <input 
        v-model="userInput" 
        @keyup.enter="submitInput"
        placeholder="输入你的问题..."
        ref="inputField"
      />
      <div v-if="loading" class="loading">发送中...</div>
      <div v-if="response" class="response">{{ response }}</div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, nextTick } from 'vue';
import { OpenAI } from 'openai';
export default defineComponent({
  emits: ['message-sent'],
  setup(props, { emit }) {
    const visible = ref(false);
    const userInput = ref('');
    const loading = ref(false);
    const response = ref('');
    const inputField = ref<HTMLInputElement | null>(null);

    const open = () => {
      visible.value = true;
      userInput.value = '';
      response.value = '';
      nextTick(() => {
        inputField.value?.focus();
      });
    };

    const close = () => {
      visible.value = false;
    };

    const submitInput = async () => {
      if (!userInput.value.trim()) return;
      loading.value = true;
      try {
        const client = new OpenAI({
          apiKey: import.meta.env.VITE_MODELSCOPE_API_KEY,
          baseURL: "https://api.modelscope.cn/v1"
        });
        
        const completion = await client.chat.completions.create({
          model: "gpt-3.5-turbo",
          messages: [{ role: "user", content: userInput.value }]
        });
        
        response.value = completion.choices[0]?.message?.content || '无响应';
      } catch (error) {
        console.error('API请求失败:', error);
        response.value = '请求失败，请重试';
      } finally {
        loading.value = false;
      }
      emit('message-sent', userInput.value);
    };

    return {
      visible,
      userInput,
      loading,
      response,
      inputField,
      open,
      close,
      submitInput
    };
  }
});
</script>
