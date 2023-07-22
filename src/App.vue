<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const bitly_token=import.meta.env.VITE_BITLY_TOKEN;
const api = "https://api-ssl.bitly.com/v4";
const url = ref(null);
const shortUrl = ref(null);
const headers = {
  Authorization: "Bearer "+bitly_token,
};

const group_guid = ref(null);

async function submit() {
  const response = await axios.get(api + "/groups", { headers });
  group_guid.value = response.data.groups[0].guid;

  const response2 = await axios.post(
    api + "/shorten",
    {
      group_guid: group_guid.value,
      domain: "bit.ly",
      long_url: url.value,
    },
    { headers }
  );
  shortUrl.value = response2.data.link;
}
</script>

<template>
  <div class="center-container">
    <div class="container">
      <label>Url to shorten:</label>
      <div class="textarea-container">
        <textarea
          v-model="url"
          class="responsive-textarea"
          placeholder="Enter your url"
        ></textarea>
      </div>
      <button type="submit" @click="submit">
        <span class="hidden-visually">Shorten</span>
      </button>
      <div v-if="shortUrl" class="short-url">{{ shortUrl }}</div>
    </div>
  </div>
</template>


<style scoped>
.center-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh; /* Center vertically on the screen */
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  max-width: 500px;
  width: 100%;
}

.textarea-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 10px;
}

.responsive-textarea {
  width: 100%;
  height: 150px;
  resize: none;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px;
  outline: none;
}

button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}

.short-url {
  margin-top: 10px;
  font-size: 16px;
  color: #007bff;
}

/* Responsive styles */
@media (max-width: 768px) {
  .responsive-textarea {
    height: 120px;
  }
}

@media (max-width: 480px) {
  .responsive-textarea {
    height: 100px;
  }
}
</style>
