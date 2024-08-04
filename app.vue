<template>
  <div>
    <h1>Email Response Generator</h1>
    <form @submit.prevent="generateResponses">
      <div class="option-group">
        <label>Casual or Formal:</label>
        <div class="toggle-switch">
          <div :class="{ 'toggle-on': style === 'casual', 'toggle-off': style === 'formal' }"
               @click="style = (style === 'casual') ? 'formal' : 'casual'">
            <div class="toggle-circle"></div>
          </div>
          <span>{{ style === 'casual' ? 'Casual' : 'Formal' }}</span>
        </div>
      </div>

      <div class="option-group">
        <label>Positive or Negative:</label>
        <div class="toggle-switch">
          <div :class="{ 'toggle-on': tone === 'positive', 'toggle-off': tone === 'negative' }"
               @click="tone = (tone === 'positive') ? 'negative' : 'positive'">
            <div class="toggle-circle"></div>
          </div>
          <span>{{ tone === 'positive' ? 'Positive' : 'Negative' }}</span>
        </div>
      </div>

      <div class="option-group">
        <label>Aggressive or Friendly:</label>
        <div class="toggle-switch">
          <div :class="{ 'toggle-on': manner === 'friendly', 'toggle-off': manner === 'aggressive' }"
               @click="manner = (manner === 'friendly') ? 'aggressive' : 'friendly'">
            <div class="toggle-circle"></div>
          </div>
          <span>{{ manner === 'friendly' ? 'Friendly' : 'Aggressive' }}</span>
        </div>
      </div>

      <div class="option-group">
        <label>Language:</label>
        <div class="toggle-switch">
          <div :class="{ 'toggle-on': language === 'english', 'toggle-off': language === 'arabic' }"
               @click="language = (language === 'english') ? 'arabic' : 'english'">
            <div class="toggle-circle"></div>
          </div>
          <span>{{ language === 'english' ? 'English' : 'Arabic' }}</span>
        </div>
      </div>

      <div class="option-group">
        <label>Email Subject:</label>
        <input type="text" v-model="subject" />
      </div>

      <button type="submit">Generate Responses</button>
    </form>

    <div v-if="responses.length > 0">
      <h2>Generated Responses:</h2>
      <div v-for="(resp, index) in responses" :key="index">
        <textarea :value="resp" readonly></textarea>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const style = ref('casual')
const tone = ref('positive')
const manner = ref('friendly')
const language = ref('english')
const subject = ref('')
const responses = ref([])

const generateResponses = async () => {
  responses.value = []

  const prompt = `Write a ${style.value} and ${tone.value} email response that is ${manner.value} in ${language.value}. The response should be written without a subject line or salutation. Only include the body of the email.`

  try {
    for (let i = 0; i < 3; i++) {
      const res = await axios.post('https://api.openai.com/v1/chat/completions', {
        model: 'gpt-3.5-turbo',
        messages: [
          { role: 'system', content: 'You are an email assistant.' },
          { role: 'user', content: prompt }
        ],
        max_tokens: 150,
        temperature: 0.7
      }, {
        headers: {
          'Authorization': `Bearer		 (KEY)			`,
          'Content-Type': 'application/json'
        }
      });

      responses.value.push(res.data.choices[0].message.content.trim());
    }
  } catch (error) {
    console.error(error);
  }
}
</script>

<style>
.option-group {
  margin-bottom: 20px;
  display: flex;
  align-items: center;
}

.toggle-switch {
  display: flex;
  align-items: center;
  margin-left: 10px;
  cursor: pointer;
}

.toggle-switch > div {
  width: 50px;
  height: 25px;
  border-radius: 50px;
  position: relative;
  background-color: #ddd;
  transition: background-color 0.3s;
}

.toggle-circle {
  width: 23px;
  height: 23px;
  border-radius: 50%;
  background-color: white;
  position: absolute;
  top: 1px;
  left: 1px;
  transition: left 0.3s;
}

.toggle-on {
  background-color: #007bff;
}

.toggle-on .toggle-circle {
  left: 26px;
}

.toggle-off {
  background-color: #ddd;
}

.toggle-off .toggle-circle {
  left: 1px;
}

textarea {
  width: 100%;
  height: 150px;
  margin-top: 20px;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}
</style>
