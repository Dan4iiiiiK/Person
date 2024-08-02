<script>
import AppAlert from './components/AppAlert.vue';
import AppList from './components/AppList.vue';
import axios from 'axios'
import AppLoader from './components/AppLoader.vue';

  export default {
    data() {
      return {
        name: '',
        people: [],
        alert: null,
        loading: false,
      }
    },
    mounted() {
      this.loadPeople()
    },
    methods: {
      async createPerson() {
        const response = await fetch('https://vue-http-70ba6-default-rtdb.firebaseio.com/people.json', {
          method: 'POST',
          headers: {
            'Content-type': 'application/json'
          },
          body: JSON.stringify({
            firstName: this.name
          })
        })

        const firebaseData = await response.json()

        this.people.push({
          firstName: this.name,
          id: firebaseData.name
        })
        this.name = ''
      },
      loadPeople() {
        this.loading = true
        setTimeout( async () => {  
          try {
            const {data} = await axios.get('https://vue-http-70ba6-default-rtdb.firebaseio.com/people.json')
            const res = Object.keys(data).map(key => {
            return {
              id: key,
              ...data[key]
              }
            })
            this.loading = false
            this.people = res
          } catch(e) {
            this.alert = {
            type: "danger",
            title: 'Ошибка',
            text: 'На сервере нет ни одного человека'
          }
          this.loading = false
          }
          }, 1500)     
        }, 
      async removePerson(id) {
        try {
          const personName = this.people.find(person => person.id === id).firstName
          await axios.delete(`https://vue-http-70ba6-default-rtdb.firebaseio.com/people/${id}.json`)
          this.people = this.people.filter(person => person.id !== id)
          this.alert = {
            type: "primary",
            title: 'Успешно!',
            text: `Пользователь ${personName}, успешно удалён`
          }
        } catch {

        }
      }
    },
    components: {AppList, AppAlert, AppLoader}
  }
</script>

<template>
  <div class="container">
    <AppAlert :alert="alert" @close="alert = null"></AppAlert>
    <form class="card" @submit.prevent="createPerson">
      <h2>Работа с базой данных</h2>

      <div class="form-control">
        <label for="name">Введите имя</label>
        <input type="text" id="name" v-model.trim="name">
      </div>

      <button type="submit" class="btn primary" :disabled="name.length === 0">Создать человека</button>
    </form>
    <AppLoader v-if="loading"></AppLoader>
    <AppList :people="people" @load="loadPeople" @remove="removePerson" v-else></AppList>
  </div>
</template>

