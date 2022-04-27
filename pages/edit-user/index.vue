<template>
  <div class="wrapper-create">
    <h2>EDIT USER</h2>
    <div v-if="loadingUser">LOADING USER DATA...</div>
    <div v-if="!loadingUser" class="div-form">
      <form class="form-wrapper" action="">
        <div class="inputs-wrapper" v-for="(inp, id) in arrayInputs" :key="id">
          <div>{{ inp.toUpperCase() }}</div>
          <input type="text" v-model="input[`${inp}`]" />
        </div>
      </form>
      <button @click="edit()">CONFIRM CHANGE</button>
      <button @click="handleBack()">CANCEL</button>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import axios from 'axios'
export default Vue.extend({
  data() {
    return {
      loadingUser: true,
      input: {
        name: '',
        gender: '',
        email: '',
        status: '',
      },
      arrayInputs: ['name', 'gender', 'email', 'status'],
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
            this.input = res.data
            this.loadingUser = false
          }
        })
        .catch(() => {
          this.loadingUser = false
        })
    },
    async edit() {
      await axios
        .put(
          `https://gorest.co.in/public/v2/users/${this.$route.query.id}`,
          this.input,
          {
            headers: {
              Accept: 'application/json',
              Authorization: `Bearer 9690fb8196780608aa119e9cadbf3901b8d6679995f2667a259a1f07fb7617cd`,
            },
          }
        )
        .then((res) => {
          if (res.status === 200) {
            this.$router.push('/')
          }
        })
        .catch((err) => {
          return err
        })
    },
    async handleBack() {
      this.$router.back()
    },
    async handleInput(field: 'name' | 'gender' | 'email' | 'status', ev: any) {
      this.input =
        field === 'name'
          ? { ...this.input, name: ev.target.value }
          : field === 'gender'
          ? { ...this.input, gender: ev.target.value }
          : field === 'email'
          ? { ...this.input, email: ev.target.value }
          : { ...this.input, status: ev.target.value }
    },
  },
  mounted() {
    this.getUser()
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
input {
  outline: none;
  border: none;
  border-bottom: 2px solid #333;
}
input:focus {
  border-bottom: 2px solid darkcyan;
}
.wrapper-create {
  padding: 24px;
}
.div-form {
  display: grid;
  gap: 24px;
}
.form-wrapper {
  display: grid;
  gap: 24px;
}
.inputs-wrapper {
  display: grid;
  gap: 12px;
}
</style>
