<script setup>
import {ref,onMounted,computed,watch} from 'vue'
import './main.css'
const todos =ref([])
const name=ref('')
const newTodo = ref('')
const category = ref('Personal')
const HideCompleted = ref(false)
const addTodo =()=>{
  if(newTodo.value.trim()===''){
    return
  }
  todos.value.push({
    content : newTodo.value,
    category : category.value,
    done : false,
    created_at :new Date().getTime()
  })
}
const todos_asc = computed(()=>{
  todos.value.sort((a,b)=>{
    return a.created_at - b.created_at
  })
})
const remove_todo = (todo)=>{
    todos.value= todos.value.filter((t)=> t!==todo)
}
const Filter_completed = computed(() => {
  const filteredTodos = HideCompleted.value
    ? todos.value.filter(t => !t.done)
    : todos.value;

  return filteredTodos.slice().sort((a, b) => b.created_at - a.created_at);
});

watch(todos,(newVal)=>{localStorage.setItem('todos',JSON.stringify(newVal))},{deep:true})
watch(name,(newVal)=>{localStorage.setItem('name',newVal)})
onMounted(()=>{name.value = localStorage.getItem('name') || ''})
onMounted(()=>todos.value = JSON.parse(localStorage.getItem('todos')) || [])
</script>
<template>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">

  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's App, 
        <input type="text" v-model="name" class="name-input" />
      </h2>
    </section>
    
    <section class="create-todo">
      <h3 class="create-todo-title">Create a Todo</h3>
      <form @submit.prevent="addTodo" class="todo-form">
        <input type="text" v-model="newTodo" placeholder="Enter a new todo" class="new-todo-input" />
        <h3 class="category-title">Pick a Category</h3>
        <select v-model="category" class="category-select">
          <option value="">Select a category</option>
          <option value="Personal">Personal</option>
          <option value="Work">Work</option>
          <option value="Other">Other</option>
        </select>
        <button type="submit" class="submit-button">
          <i class="fas fa-plus"></i> Add
        </button>
      </form>
      
      <div class="todos-list">
        <ul>
          <li v-for="todo in Filter_completed" :key="todo.created_at" class="todo-item">
            <div class="checkbox-wrapper-18">
         <div class="round">
             <input type="checkbox" :id="'checkbox-' + todo.created_at" v-model="todo.done" />
            <label :for="'checkbox-' + todo.created_at"></label>
  </div>
</div>
        
            <input type="text" v-model="todo.content" class="todo-content" />
            <span class="todo-category">{{ todo.category }}</span>
            <button @click="remove_todo(todo)" class="delete-button">
              <i class="fas fa-trash"></i>
            </button>
          </li>
        </ul>
      </div>
      
      <div class="toggle-button">
        <button @click="HideCompleted = !HideCompleted" class="toggle-completed-button">
          <i class="fas" :class="HideCompleted ? 'fa-eye' : 'fa-eye-slash'"></i>
          {{ HideCompleted ? 'Show All' : 'Hide Completed' }}
        </button>
      </div>
    </section>
  </main>
</template>
