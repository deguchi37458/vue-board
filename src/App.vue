<template>
  <div class="back">
    <div class="wrapper">
      <div class="post" v-for="post in posts" :key="post.name">
        <span class="name">{{post.fields.name.stringValue}}</span>
        <span class="date">{{post.fields.created.timestampValue | dateFilter}}</span>
        <p class="comment">{{post.fields.comment.stringValue}}</p>
      </div>
      <button @click="submitPosts">書き込む</button><span>名前：</span><input type="text" v-model="name"><br>
      <textarea v-model="comment"></textarea>
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
      if( this.name === ""){
        this.name = "名無し";
      }
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
.date {

}


</style>