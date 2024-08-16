<script setup>
  import { ref, computed } from 'vue'

  let id = 0
  const newTodo = ref('')
  const status = ref(0)
  const editId = ref(null)

  const todos = ref([
    { id: id++, text: 'Learn HTML', done: true },
    { id: id++, text: 'Learn JavaScript', done: true },
    { id: id++, text: 'Learn Vue', done: false }
  ])

  function addTodo() {
    todos.value.push({ id: id++, text: newTodo.value, done: false })
    newTodo.value = ''
  }

  function removeTodo(todo) {
    todos.value = todos.value.filter((t) => t !== todo)
  }

  function editTodo(todo) {
    editId.value = todo.id;
  }

  function saveEdit(todo) {
    const index = todos.value.findIndex(t => t.id === todo.id);
    if (index !== -1) {
      todos.value[index] = { ...todo, text: todo.text };
      editId.value = null;
    }
  }

  const filteredTodos = computed(() => {
    if (status.value === 0) {
      return todos.value.filter(todo => todo)
    } else if (status.value === 1) {
      return todos.value.filter(todo => !todo.done)
    } else if (status.value === 2) {
      return todos.value.filter(todo => todo.done)
    }
    return [];
  })

  const setFilter = (filterStatus) => {
    status.value = filterStatus;
  };
</script>

<template>
  <div class="todo">
    <div class="todo-wrapper">
      <h1 class="todo-title">Todos</h1>
      <div class="todo-inner">
        <div class="todo-add">
          <form @submit.prevent="addTodo">
            <input v-model="newTodo" type="text" placeholder="Add new todo">
          </form>
        </div>
        <ul class="todo-list">
          <li v-for="todo in filteredTodos" :key="todo.id" class="todo-item">
            <input class="todo-check" type="checkbox" :id="todo.id" v-model="todo.done">
            <label class="todo-label" :for="todo.id" :class="{ selected: todo.done }">
              <input class="label-input" type="text" v-if="editId === todo.id" v-model="todo.text" :placeholder="todo.text"
              @blur="saveEdit(todo)"
              @keyup.enter="saveEdit(todo)"
            />
            <span v-else>{{ todo.text }}</span>
            </label>
            <button v-if="!todo.done" class="todo-edit" @click="editTodo(todo)">
              <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT-SyD2TR14xu4ygHex-2-NReYfrhJa5JmKiw&s" alt="pencil">
            </button>
            <button class="todo-remove" @click="removeTodo(todo)">
              <img src="https://static.vecteezy.com/system/resources/previews/037/752/223/non_2x/trash-icon-for-web-app-uiux-infographic-etc-vector.jpg" alt="trash">
            </button>
          </li>
        </ul>
        <div class="todo-footing">
          <ul class="footing-lits">
            <li class="footing-item" :class="{ selected: status === 0 }" @click="setFilter(0)">All</li>
            <li class="footing-item" :class="{ selected: status === 1 }" @click="setFilter(1)">Active</li>
            <li class="footing-item" :class="{ selected: status === 2 }" @click="setFilter(2)">Completed</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .todo-title {
    margin-top: 0;
    margin-bottom: 50px;
  }

  .todo {
    background-color: rgb(240, 200, 240);
    width: 100%;
    height: 100vh;
    padding: 150px 100px;
  }

  .todo-inner {
    width: 600px;
    margin: 0 auto;
  }

  .todo-list {
    list-style-type: none;
    padding-left: 0;
    margin-bottom: 0;
  }

  .todo-item {
    padding-right: 40px;
    position: relative;
    display: flex;
    padding: 10px 80px 10px 20px;
    align-items: center;
    background-color: #fff;
    margin-bottom: 20px;
    border-radius: 5px;
    box-shadow: 1px 1px 5px #3f3c3c;
  }

  .todo-check {
    position: absolute;
    opacity: 0;
    width: 0;
    height: 0;
  }

  .todo-check:checked + .todo-label::before {
    border: 1px solid rgb(240, 200, 240);
    background: rgb(240, 200, 240) url("https://static-00.iconduck.com/assets.00/tick-01-icon-2048x1653-m70buoup.png") center center / 100% no-repeat;
  }

  .todo-label {
    position: relative;
    padding-left: 22px;
    cursor: pointer;
    text-align: left;
    padding: 10px 0 10px 20px;
  }

  .todo-label.selected span {
    text-decoration: line-through;
  }

  .label-input {
    color: #000;
    border: 1px solid;
  }

  .todo-label::before {
    content: "";
    top: 50%;
    left: 0;
    width: 14px;
    height: 14px;
    border: 1px solid #3f3c3c;
    background: #fff no-repeat center center / 90% auto;
    display: inline-block;
    position: absolute;
    transform: translateY(-50%);
  }

  .todo-remove {
    border: 0;
    padding: 0;
    width: 20px;
    position: absolute;
    right: 20px;
    top: 50%;
    transform: translateY(-50%);
    cursor: pointer;
    opacity: 1;
    transition: opacity .3s ease;
  }

  .todo-edit:hover,
  .todo-remove:hover {
    opacity: .8;
  }

  .todo-edit img,
  .todo-remove img {
    width: 100%;
    object-fit: cover;
  }

  .todo-edit {
    border: 0;
    padding: 0;
    width: 20px;
    position: absolute;
    right: 50px;
    top: 50%;
    transform: translateY(-50%);
    cursor: pointer;
    opacity: 1;
    transition: opacity .3s ease;
  }

  .todo-add input {
    width: 100%;
    height: 50px;
    background-color: #fff;
    padding: 10px 20px
  }

  .todo-add input:focus {
    outline-color: #c15dff;
  }

  .todo-footing {
    background-color: #fff;
    padding: 20px;
  }

  .footing-lits {
    list-style-type: none;
    padding-left: 0;
    margin: 0;
    display: flex;
    justify-content: center;
  }

  .footing-item {
    margin: 0 10px;
    cursor: pointer;
    transition: color .3s ease;
  }

  .footing-item:hover {
    color: rgb(238, 94, 113);
  }

  .footing-item.selected {
    font-weight: 700;
    cursor: default;
    color: rgb(238, 94, 113);
  }
</style>
