<template>
  <amplify-authenticator>
    <h1>Monthly Sample Mobile App</h1>
    <v-text-field v-model="name" label="Name"></v-text-field>
    <v-text-field v-model="description" label="Description"></v-text-field>
    <v-btn @click="createTodo">Create</v-btn>
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        {{ todo.name }} : {{ todo.description }}
      </li>
    </ul>
    <amplify-sign-out></amplify-sign-out>
  </amplify-authenticator>
</template>

<script>
import { API } from 'aws-amplify'
import { createTodo } from '~/src/graphql/mutations'
import { listTodos } from '~/src/graphql/queries'

export default {
  data() {
    return {
      name: '',
      description: '',
      todos: [],
    }
  },
  async created() {
    await this.getTodos()
  },
  methods: {
    async createTodo() {
      const { name, description } = this
      if (!name || !description) return false
      const todo = { name, description }
      await API.graphql({
        query: createTodo,
        variables: { input: todo },
      })
      this.name = ''
      this.description = ''
      this.getTodos()
    },
    async getTodos() {
      const todos = await API.graphql({
        query: listTodos,
      })
      this.todos = todos.data.listTodos.items
    },
  },
}
</script>
