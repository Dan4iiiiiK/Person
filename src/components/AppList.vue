<template>
  <div class="container">
    <div v-if="people.length !== 0">
      <div class="card inline" v-for="person in people" :key="person.id">
        <h3><strong>{{ person.firstName }}</strong></h3>
        <button class="btn danger" @click="$emit('remove', person.id)">Удалить</button>
      </div>
    </div>
    <div v-else class="card center">
      <h3>Никого нет</h3>
      <button class="btn" @click="$emit('load')">Загрузить список</button>
    </div>
  </div>
</template>

<script>
  export default {
    emits: [
      'load',
      'remove'
    ],
    props: ['people']
  }
</script>
<style>
  .inline {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
</style>