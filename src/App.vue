<script setup>
  import { ref, watch, onMounted, computed } from 'vue'

  const todos = ref([])
  const name = ref('')


  const input_content = ref('')
  const input_category = ref(null)

  // el input mas reciente se ponga el principio de la lista
  const todos_asc = computed(() => todos.value.sort((a,b) => {
    return b.createdAt - a.createdAt
  }))

  const addTodo = () => {
    if(input_content.value.trim() === '' || input_category.value === null) return

    //Push, manda las propiedades internas
    //por eso watch no puede acceder a las propiedades internas
    //por eso hacemos uso de DEEP
    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      done: false,
      createdAt: new Date().getTime()
    })
  //leave the input clean
    input_content.value = ''
    input_category.value = null
  }
// hacer un filtro y comprar que el todo es diferente a los que estan almacenados.
// si es diferente, se añade a la lsita y si es igual, desaparece.
  const removeTodo = (todo) => {
    todos.value = todos.value.filter(t => t !== todo)
  }

  watch(todos, (newVal)=>{
    localStorage.setItem('todos', JSON.stringify(newVal))
  }, {deep: true}) // Deep, permite a watch de mirar en uno de los objetos que hemos creado y mirar cada una de las propiedades

  watch(name, (newVal) => {
    localStorage.setItem('name', newVal)
  })

  onMounted(()=> {
    name.value = localStorage.getItem('name') || ''
    todos.value = JSON.parse(localStorage.getItem('todos'))
  })
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TO DO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your to do list?</h4>
        <input type="text" placeholder="e.g make a video" v-model="input_content">
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category">
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add to do">
      </form>


    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>


