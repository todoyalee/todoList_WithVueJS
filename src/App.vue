<script setup>
import { ref,onMounted,computed,watch} from 'vue'
 //these are  different things u can use with the compostion API in vuejs : refereacne (ref) will handole our state
 // onMounted used when we firs render the page (or the component)
 //we're gonna use onMounted to get our localstorage out of localstorage 
 // computer to have thigns in order(a to b)(it's ordered by the data it's created)
 //watch : watch for any changes in our referencees  and if they update , it will update in localstorage

 const todos=ref([])
 const name= ref('')

 const input_content=ref('')
 const input_category=ref(null)

 const todos_async=computed(()=>todos.value.sort((a,b)=>{
  return b.createdAt - a.createdAt
  //this should give us asyndicne list by date
 }))
 const addTodo=()=> {
  if(input_content.value.trim() === '' || input_category.value=== null){
    return 
  }
  todos.value.push({

  
    content: input_content.value,
    category:input_category.value,
    done:false, 
    createdAt: new Date().getTime() 
    
  })
  input_content.value= ''
  input_category=null
}

 const removeTodo  = todo =>  {
  todos.value  = todos.value.filter(t => t!== todo)
 }
 watch(todos, newVal =>{
  localStorage.setItem('todos',JSON.stringify(newVal))
  //set pout initial todos -> work but don't for push
 },
 {deep:true}
 //deep catch changes
 )

 watch(name,(newVal)=>{
  localStorage.setItem('name',newVal) 
 })
 onMounted(()=>{
  name.value=localStorage.getItem('name') || ''
  todos.value=JSON.parse(localStorage.getItem('todos')) || []
 })
 //we just did the filtering

</script>


<template>
<main class="app">
  <section name="greeting">
    <h2 class="title">
      What's up , <input type="text" placeholder="name here" v-model="name" />
    </h2>
  </section>

  <section class="create-todo">
    <h3>CREATE A TODO</h3>

    <form @submit.prevent="addTodo">
    <h4> WHAT'S ON YOUR TODO LIST ?</h4>    
    <input type="text" placeholder=
    "make a video" v-model="input_content"/>
      <h4>Pick  a category</h4>  
      <div class="options">
        <label >
          <input 
          type="radio" name="category" 
          value="business"
           v-model="input_category" />
           <span class="bubble business"></span>
            <div>business</div>
        </label>
        <label >
          <input 
          type="radio" name="category" 
          value="personal"
           v-model="input_category" />
           <span class="bubble personal"></span>
            <div>Personal</div>
        </label>
      </div>
      <input type="submit" value="Add  todo " />

    
  </form>
  </section>
  <section class="todo-list">
    <h3>TODO LIST</h3>
    <div class="list">
      <div v-for="todo in todos_async" :class="`todo-item ${  todo.done && 'done '   }`">
      <label >
        <input type="checkbox" v-model="todo.done" />

        <span :class="`bubble ${todo.category}` "></span>
      </label>

<div class="todo-content">
  <input type="text" v-model="todo.content" />
</div>
<div class="actions">
  <button class ="delete" @click="removeTodo(todo)" > Delete</button>
</div>


      </div>
    </div>
  </section>
</main>
</template>