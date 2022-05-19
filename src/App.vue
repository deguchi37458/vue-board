<template>
  <div class="back">
    <div class="">
      <p>送信:</p>
      <p>{{ sendMessage }}</p>
      <textarea v-model="message"></textarea>
      <button @click="sendChat">送信</button>
    </div>
  </div>
</template>

<script>


export default {
  data() {
    return {
      message: '',
      sendMessage: ''
    }
  },
  methods: {
    sendChat() {
      this.sendMessage = this.message
      this.message = ''
    }
  }
}
</script>

<style scoped>
@import './assets/css/destyle.css';
@import './assets/css/common.css';


</style>