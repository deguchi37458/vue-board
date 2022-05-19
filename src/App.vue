<template>
  <div class="back">
    <h1>掲示板！</h1>
    名前
    <div><input type="text" v-model="name"></div>
    コメント
    <div><textarea v-model="comment"></textarea></div>
    <br>
    <button @click="submitPosts">投稿する</button>
    <br><br>
    <h2>投稿一覧</h2>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      name: '',
      comment: ''
    }
  },
  methods: {
    submitPosts() {
      axios.post(
        "https://firestore.googleapis.com/v1/projects/vue-board-7fd17/databases/(default)/documents/posts",
        {
          fields: {
            name: {
              stringValue: this.name
            },
            comment: {
              stringValue: this.comment
            }
          }
        }
      ).then(() => {
        this.name = '';
        this.comment = '';
      });
    }
  }
}
</script>

<style scoped>
@import './assets/css/destyle.css';
@import './assets/css/common.css';


</style>