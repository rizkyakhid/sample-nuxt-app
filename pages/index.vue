<template>
  <div class="wrapper">
    <div class="buttons">
      <button>ADD USER</button>
    </div>
    <div v-if="loadingUser">LOADING USER...</div>
    <div v-if="users && !loadingUser" class="table-wrapper">
      <table>
        <thead>
          <td
            class="tdata text-center"
            v-for="(thead, index) in theadData"
            :key="index"
          >
            {{ thead }}
          </td>
        </thead>
        <tr v-for="(user, index) in users" :key="index">
          <td class="tdata" v-for="(tDatum, index) in tData" :key="index">
            {{ user[`${tDatum}`] }}
          </td>
          <td class="tdata text-center">
            <button class="btn-actions">UPDATE</button>
            <button class="btn-actions" @click="deleteUser(user.id)">
              DELETE
            </button>
            <button
              class="btn-actions"
              @click="handleRoute(user.id.toString())"
            >
              DETAILS
            </button>
          </td>
        </tr>
      </table>
      <div class="pagination">
        <button @click="handlePage('prev')">PREV</button>
        <button v-if="page > 3" disabled="disabled">...</button>
        <button
          v-for="(pg, id) in pagiantionData"
          :key="id"
          @click="handlePage(pg)"
          v-bind:class="{ 'btn-active': page === pg }"
        >
          {{ pg }}
        </button>
        <button disabled="disabled">...</button>
        <button @click="handlePage('next')">NEXT</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import axios from 'axios'

export default Vue.extend({
  name: 'IndexPage',
  data() {
    return {
      loadingUser: true,
      users: [] as any,
      page: 1,
      theadData: ['ID', 'NAME', 'EMAIL', 'GENDER', 'STATUS', 'ACTIONS'],
      tData: ['id', 'name', 'email', 'gender', 'status'],
      pagiantionData: [1, 2, 3, 4],
    }
  },
  methods: {
    async getUsers() {
      this.loadingUser = true
      await axios
        .get('https://gorest.co.in/public/v2/users', {
          headers: { Accept: 'application/json' },
          params: { page: this.page },
        })
        .then((res) => {
          if (res.status === 200) {
            this.users = res.data
            this.loadingUser = false
          }
        })
        .catch((err) => {
          this.users = err
          this.loadingUser = false
        })
    },
    async deleteUser(id: number) {
      await axios
        .delete(`https://gorest.co.in//public/v2/users/${id}`, {
          headers: {
            Accept: 'application/json',
            Authorization: `Bearer 9690fb8196780608aa119e9cadbf3901b8d6679995f2667a259a1f07fb7617cd`,
          },
        })
        .catch((err) => {
          return err
        })
      await this.getUsers()
    },
    async handlePage(value: number | 'prev' | 'next') {
      if (value === 'prev') {
        this.page = this.page - 1
      } else if (value === 'next') {
        this.page = this.page + 1
      } else {
        this.page = value
      }
      if (this.page <= 3) {
        this.pagiantionData = [1, 2, 3, 4]
      } else {
        this.pagiantionData = [this.page - 1, this.page, this.page + 1]
      }
      await this.getUsers()
    },
    async handleRoute(params: string) {
      this.$router.push({ path: 'details', query: { id: params } })
    },
  },
  mounted() {
    this.getUsers()
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
.wrapper {
  padding: 24px;
  display: grid;
  gap: 12px;
}
.pagination {
  gap: 12px;
}
.table-wrapper {
  display: grid;
  gap: 12px;
}
.tdata {
  padding: 8px;
  border: 1px solid #000;
}
.text-center {
  text-align: center;
}
.btn-active {
  background: darkcyan;
  color: #fff;
}
.btn-actions {
  font-size: 12px;
}
</style>
