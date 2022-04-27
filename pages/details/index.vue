<template>
  <div class="wrapper-details">
    <div v-if="loadingUser">LOADING DETAILS...</div>
    <div v-if="!loadingUser" class="general-info">
      <h2>{{ user.name }}</h2>
      <div
        v-for="(item, index) in userData"
        :key="index"
        class="info-wrapper"
        v-bind:class="{ 'bg-even': index % 2 === 0 }"
      >
        <div>
          {{ `${item.toUpperCase()}` }}
        </div>
        <div>
          {{ user[`${item}`] }}
        </div>
      </div>
      <button @click="handleBack()">BACK</button>
    </div>
    <div v-if="posts.length !== 0 && !loadingPost" class="post-wrapper">
      <h5>USER'S POSTS</h5>
      <div v-for="(post, id) in posts" :key="id">
        <h6>{{ post.title }}</h6>
        <p>{{ post.body }}</p>
      </div>
    </div>
    <div v-if="posts.length === 0 && !loadingPost">
      <h5>THIS USER HASN'T POSTED ANYTHING YET</h5>
    </div>
  </div>
</template>

<script lang="ts">
import axios from 'axios'
import Vue from 'vue'

export default Vue.extend({
  data() {
    return {
      loadingUser: true,
      loadingPost: true,
      posts: [] as any,
      user: [] as any,
      userData: ['id', 'email', 'gender', 'status'],
    }
  },
  methods: {
    async getUser() {
      this.loadingUser = true
      await axios
        .get(`https://gorest.co.in/public/v2/users/${this.$route.query.id}`, {
          headers: { Accept: 'application/json' },
        })
        .then((res) => {
          if (res.status === 200) {
            this.user = res.data
            this.loadingUser = false
          }
        })
        .catch((err) => {
          this.user = err
          this.loadingUser = false
        })
    },
    async getPosts() {
      this.loadingPost = true
      await axios
        .get(
          `https://gorest.co.in/public/v2/users/${this.$route.query.id}/posts`,
          {
            headers: { Accept: 'application/json' },
          }
        )
        .then((res) => {
          if (res.status === 200) {
            this.posts = res.data
            this.loadingPost = false
          }
        })
        .catch((err) => {
          this.posts = err
          this.loadingPost = false
        })
    },
    async handleBack() {
      this.$router.back()
    },
  },
  mounted() {
    this.getUser()
    this.getPosts()
    console.log(this.posts, 'INIPOSTS')
  },
})
</script>

<style lang="css">
* {
  color: #333;
}
button {
  border-radius: 12px;
  outline: none;
  border: none;
  font-size: 16px;
  padding: 4px 8px;
}
button:hover {
  background: darkcyan;
  color: #fff;
}
.wrapper-details {
  padding: 24px;
  display: grid;
  gap: 24px;
}
.general-info {
  display: grid;
  gap: 12px;
}
.info-wrapper {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px;
}
.bg-even {
  background-color: whitesmoke;
}
.post-wrapper {
  display: grid;
  gap: 12px;
}
</style>
