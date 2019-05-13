<template lang="pug">
  div(v-cloak="")
    div.card
      header.card-header
        p.card-header-title Users
      div.card-content
        table.table.is-bordered.is-fullwidth
          tbody
            tr(v-for="user in users")
              td {{ user.username }}
        div.field.has-addons
          div.control.is-expanded
            input.input(
              type="text"
              placeholder="Input username"
              v-model="username"
              v-debounce="fetch"
              autocomplete="on"
              list="usernames"
            )
          datalist#usernames
            option(v-for="user in candidates", v-bind:key="user.id" v-bind:value="user.username")
          div.control
            a.button.is-primary(v-on:click="addUser()" :disabled="existsUser") Add user
</template>

<script>
import HTTP from "./http";
import axios from "axios";

export default {
  data: () => ({
    username: "",
    project: Object,
    candidates: [],
    users: []
  }),

  created() {
    HTTP.get('users').then(response => {
      this.users = response.data;
      console.log(this.users)
    });
  },

  computed: {
    existsUser() {
      for (var user of this.candidates) {
        if (user.username === this.username) {
          return false;
        }
      }
      return true;
    }
  },

  methods: {
    fetch(value) {
      axios.get(`/v1/users?q=${this.username}`).then(response => {
        this.candidates = response.data;
      });
    },
    addUser() {
      const user = this.candidates.find(
        user => user.username === this.username
      );
      const payload = {
        user_id: user.id
      }
      HTTP.post("users", payload).then(response => {
        console.log("Add user");
      });
    },
    removeUser(user) {
      HTTP.delete(`users/${user.id}`).then(response => {
        console.log("Add user");
      });
    }
  }
};
</script>
