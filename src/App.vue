<template>
  <div class="back">
    <div class="wrapper">
      <header>
        <a href="">戻る</a>
        <a href="https://github.com/deguchi37458/vue-board" target="_blank">【PR】GitHub</a>
      </header>

      <h1 class="title">Vueで懐かしい掲示板を作ってみたスレ</h1>
      <div class="post" v-for="post in posts" :key="post.name">
        <span class="num">{{post.fields.num.integerValue}}:</span>
        <span class="name">{{post.fields.name.stringValue}}:</span>
        <span class="date">{{post.fields.created.timestampValue | dateFilter}}</span>
        <p class="comment">{{post.fields.comment.stringValue}}</p>
      </div>
      <button @click="submitPosts">書き込む</button><span>名前：</span><input type="text" v-model="name"><br>
      <textarea v-model="comment"></textarea>

      <footer>
        
      </footer>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment';

export default {
  data() {
    return {
      num: 0,
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
      this.num = this.num + 1;
      if( this.name === ""){
        this.name = "名無し";
      }
      axios.post(
        "https://firestore.googleapis.com/v1/projects/vue-board-7fd17/databases/(default)/documents/posts",
        {
          fields: {
            num: {
              integerValue: this.num
            },
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
        function compare( a, b ){
          var r = 0;
          if( a.fields.created.timestampValue < b.fields.created.timestampValue ){ r = -1; }
          else if( a.fields.created.timestampValue > b.fields.created.timestampValue ){ r = 1; }
          return r;
        }
        res.data.documents.sort(compare);
        
        this.posts = res.data.documents;
        console.log(res.data.documents);

      });
    }
  },
  filters: {
    dateFilter(date) {
      moment.locale("ja");
      return moment(date).format('YYYY/MM/DD(ddd) HH:mm:ss');
    }
  }
};
</script>

<style scoped>
@import './assets/css/common.css';
.title {
  color: red;
  font-size: 16px;
  margin-bottom: 20px;
}
.post {
  margin-bottom: 20px;
}
.name {
  color: green;
  font-weight: bold;
}
.comment {
  margin-left: 30px;
}


</style>