<template>
  <v-app>
    <v-app-bar
        app
        color="primary"
        dark
    >
      <div class="d-flex align-center">
        <v-img
            alt="Vuetify Logo"
            class="shrink mr-2"
            contain
            src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
            transition="scale-transition"
            width="40"
        />
      </div>
      <v-spacer></v-spacer>
      Я уверен что код красивый
    </v-app-bar>
    <v-main>
      <v-container>
        <v-col>
          <v-row>
            <v-btn color="primary" class="mr-2" @click="initWorkingData">Создать данные для работы</v-btn>
            <v-btn color="primary" @click="initUsers">Получить пользователей из API</v-btn>
          </v-row>
        </v-col>

        <v-col>
          <v-row class="mt-2" v-if="users.length">
            Мои пользователи:
          </v-row>
          <v-row>
            <v-chip
                :key="user.id"
                v-for="user in users"
                close
                @click="editableUser = {...user}"
                class="mt-2 mr-1"
                @click:close="deleteUser(user._id)"
            >
              {{user.name}}
            </v-chip>
          </v-row>
          <v-row v-if="editableUser">
            <v-col class="pl-0" cols="6">
              <v-text-field
                  outlined
                  dense
                  hide-details
                  class="col-3"
                  v-model="editableUser.name"
              />
            </v-col>
            <v-col cols="6">
              <v-btn @click="saveEditableUser" color="primary">
                Сохранить
              </v-btn>
            </v-col>
          </v-row>
        </v-col>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>

export default {
  name: 'App',
  components: {},

  data: () => ({
    apiUrl: 'https://crudcrud.com/api/1856408da3374e3c859236a642deaa8b',
    users: [],
    editableUser: null,
  }),
  methods: {
    initUsers(){
      this.$axios.get(`${this.apiUrl}/users`).then(response => {
          this.users = response.data;
      }).catch(e => console.log(e))
    },
    initWorkingData() {
      const users = this.getRandomUsers(10);
      users.forEach(user => {
        this.$axios.post(`${this.apiUrl}/users`, user).catch(e => console.log(e))
      });
    },
    getRandomUsers(count){
      const users = [];
      for (let i = 0; i < count; i++) {
        users.push(this.getRandomUser())
      }
      return users;
    },
    getRandomUser() {
      const firstNames = ["Владислав", "Иван", "Петр", "Василий", "Владимир"];
      const lastNames = ["Мудрый", "Сильный", "Великий", "Пупкин", "Сергиенко"];
      const firstName = firstNames[Math.floor(Math.random() * firstNames.length)];
      const lastName = lastNames[Math.floor(Math.random() * lastNames.length)];
      return {
        name: `${firstName} ${lastName}`
      }
    },
    deleteUser(userId){
      this.$axios.delete(`${this.apiUrl}/users/${userId}`).then(() => {
        this.initUsers();
      }).catch(e => console.log(e));
    },
    saveEditableUser(){
      this.$axios.put(`${this.apiUrl}/users/${this.editableUser._id}`,{
        name: this.editableUser.name
      }).then(() => {
        this.initUsers();
        this.editableUser = null;
      }).catch(e => console.log(e));
    }
  }
};
</script>
