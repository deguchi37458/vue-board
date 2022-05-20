<template>
  <div class="back">
    <div class="wrapper">
      <div v-for="post in posts" :key="post.name">
        <p>名前：{{post.fields.name.stringValue}}</p>
        <p>コメント：{{post.fields.comment.stringValue}}</p>
        <p>投稿日時：{{post.fields.created.timestampValue | dateFilter}}</p>
      </div>
      <input type="text" v-model="name">
      <textarea v-model="comment"></textarea>
      <button @click="submitPosts">投稿する</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment';

export default {
  data() {
    return {
      name: '',
      comment: '',
      posts: ''
    }
  },
    created() {
    this.getPosts();
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
            },
            created: {
              timestampValue: new Date()
            }
          }
        }
      ).then(() => {
        this.name = '';
        this.comment = '';
        this.getPosts();
      });
    },
    getPosts() {
      axios.get(
        "https://firestore.googleapis.com/v1/projects/vue-board-7fd17/databases/(default)/documents/posts"
      )
      .then(res => {
        this.posts = res.data.documents;
        console.log(res.data.documents);
      });
    }
  },
  filters: {
    dateFilter(date) {
      return moment(date).format('YYYY/MM/DD HH:mm:ss');
    }
  }
};
</script>

<style scoped>
@import './assets/css/destyle.css';
@import './assets/css/common.css';


</style>