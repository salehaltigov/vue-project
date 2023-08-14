<script>
import axios from 'axios';

export default {
  data() {
    return {
      posts: [],
      displayedPosts: [],
      newPostTitle: "",
      newPostBody: "",
      currentPage: 1,
      postsPerPage: 6,
    };
  },
  computed: {
    isAddButtonDisabled() {
      return !this.newPostTitle || !this.newPostBody;
    },
    totalPages() {
      return Math.ceil(this.posts.length / this.postsPerPage);
    },
  },
  created() {
    this.fetchPosts();
  },
  watch: {
    posts() {
      this.changePage(this.currentPage); // Установите текущую страницу после загрузки постов
    },
  },
  methods: {
    async fetchPosts() {
      try {
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/posts",
          { params: { _limit: 20 } }
        );
        this.posts = response.data;
      } catch (error) {
        console.error("Error fetching posts:", error);
      }
    },
    async addPost() {
      try {
        const response = await axios.post(
          "https://jsonplaceholder.typicode.com/posts",
          {
            title: this.newPostTitle,
            body: this.newPostBody,
            userId: 1,
          }
        );
        this.posts.unshift(response.data);
        this.newPostTitle = "";
        this.newPostBody = "";
        this.changePage(1); // После добавления поста переключитесь на первую страницу
      } catch (error) {
        console.error("Error adding post:", error);
      }
    },
    changePage(pageNumber) {
      this.currentPage = pageNumber;
      const startIndex = (this.currentPage - 1) * this.postsPerPage;
      this.displayedPosts = this.posts.slice(startIndex, startIndex + this.postsPerPage);
    },
  },
};
</script>

<template>
  <div>
    <img class="img_logo" src="./assets/logo1.png" alt="">
    <div class="addNewPost">
      <input v-model="newPostTitle" placeholder="Заголовок" />
      <input v-model="newPostBody" placeholder="Текст поста" />
      <button @click="addPost" :disabled="isAddButtonDisabled">Добавить пост</button>
    </div>
    <h1>Статьи</h1>
    <div class="posts">
      <div v-for="(post, index) in displayedPosts" :key="post.id" class="post">
        <h2>{{ post.title }}</h2>
        <p>{{ post.body }}</p>
      </div>
    </div>
    <div class="pagination">
      <button
        v-for="pageNumber in totalPages"
        :key="pageNumber"
        @click="changePage(pageNumber)"
        :class="{ active: currentPage === pageNumber }"
      >
        {{ pageNumber }}
      </button>
    </div>
  </div>
</template>



<style scoped>
.img_logo{
  margin-top: 20px;
  margin-left: 55px;
  margin-bottom: 20px;
}

h1{
  color: #FFF;
  font-family: Montserrat;
  font-size: 25px;
  font-style: normal;
  font-weight: 700;
  line-height: 150%; /* 37.5px */
  margin-top: 20px;
  margin-left: 55px;
  margin-bottom: 20px;
}

input::placeholder{
  padding-left: 10px;
  color: #D3CCCC;
  font-family: Montserrat;
  font-size: 18px;
  font-style: normal;
  font-weight: 400;
  line-height: 150%; /* 27px */

}
.addNewPost input{   
  font-family: Montserrat;
  font-size: 18px;
  font-style: normal;
  font-weight: 400;
  line-height: 150%; /* 27px */ 
  width: 350px;
  height: 70px;
  border-radius: 5px;
  border: 1px solid #FFF;
  background: #FFF;
  margin-right: 20px;
  padding: 10px; 
  box-sizing: border-box; 

}

.addNewPost button{
  color: #FFF;
  font-family: Montserrat;
  font-size: 20px;
  font-style: normal;
  font-weight: 700;
  line-height: 150%; /* 30px */
  width: 350px;
  height: 70px;
  flex-shrink: 0;
  border-radius: 9px;
  background: #5B98E3;
}
.addNewPost{
  display: flex; 
  justify-content: center;
  align-items: center;
}
.posts {
  width: 1200px;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;

}

.post {
  width: 350px;
  height: 222px;
  padding: 10px;
  box-sizing: border-box;
  background: #fff;
  margin: 10px;
 
}

.pagination {
  display: flex;
  justify-content: center;
  margin-top: 10px;
}

.pagination button {
  margin: 0 5px;
  padding: 5px 10px;
  border-radius: 5px;
  background: #B0B4FF;
  cursor: pointer;
}

.pagination button.active {
  border-radius: 5px;
  background-color: #FFF;
  color: #000;
  border-color: #5b98e3;
}
</style>
