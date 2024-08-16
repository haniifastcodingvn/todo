<script setup>
import { ref, computed } from 'vue';

let id = 0;
const newTodo = ref('');
const status = {
  all: 'All',
  active: 'Active',
  completed: 'Completed',
};
const currentStatus = ref(status.all);
const editId = ref(null);

const todos = ref([
  { id: id++, text: 'Learn HTML', done: true },
  { id: id++, text: 'Learn JavaScript', done: true },
  { id: id++, text: 'Learn Vue', done: false },
]);

const addTodo = () => {
  if (newTodo.value.trim() === '') return;
  todos.value.push({ id: id++, text: newTodo.value, done: false });
  newTodo.value = '';
};

const removeTodo = (id) => {
  todos.value = todos.value.filter((todo) => todo.id !== id);
};

const editTodo = (todo) => {
  editId.value = todo.id;
};

const saveEdit = (index) => {
  if (todos.value[index].text.trim() === '') {
    return;
  }
  todos.value[index] = { ...todos.value[index], text: todos.value[index].text };
  editId.value = null;
};

const filteredTodos = computed(() => {
  if (currentStatus.value === status.active) {
    return todos.value.filter((todo) => !todo.done);
  } else if (currentStatus.value === status.completed) {
    return todos.value.filter((todo) => todo.done);
  } else {
    return todos.value;
  }
});

const setFilter = (filterStatus) => {
  currentStatus.value = filterStatus;
};
</script>

<template>
  <div class="todo">
    <div class="todo-wrapper">
      <h1 class="todo-title">Todos</h1>
      <div class="todo-inner">
        <div class="todo-add">
          <form @submit.prevent="addTodo">
            <input v-model="newTodo" type="text" placeholder="Add new todo" />
          </form>
        </div>
        <ul class="todo-list">
          <li v-for="(todo, index) in filteredTodos" :key="todo.id" class="todo-item">
            <input class="todo-check" type="checkbox" :id="todo.id" v-model="todo.done" />
            <label class="todo-label" :for="todo.id" :class="{ selected: todo.done }">
              <input
                class="label-input"
                type="text"
                v-if="editId === todo.id"
                v-model="todo.text"
                :placeholder="todo.text"
                @blur="saveEdit(index)"
                @keyup.enter="saveEdit(index)"
              />
              <span v-else>{{ todo.text }}</span>
            </label>
            <button v-if="!todo.done" class="todo-edit" @click="editTodo(todo)">
              <img
                src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT-SyD2TR14xu4ygHex-2-NReYfrhJa5JmKiw&s"
                alt="pencil"
              />
            </button>
            <button class="todo-remove" @click="removeTodo(todo.id)">
              <img
                src="https://static.vecteezy.com/system/resources/previews/037/752/223/non_2x/trash-icon-for-web-app-uiux-infographic-etc-vector.jpg"
                alt="trash"
              />
            </button>
          </li>
        </ul>
        <div class="todo-footing" v-if="todos.length > 0">
          <ul class="footing-lits">
            <li class="footing-item" :class="{ selected: currentStatus === status.all }" @click="setFilter(status.all)">
              {{ status.all }}
            </li>
            <li
              class="footing-item"
              :class="{ selected: currentStatus === status.active }"
              @click="setFilter(status.active)"
            >
              {{ status.active }}
            </li>
            <li
              class="footing-item"
              :class="{ selected: currentStatus === status.completed }"
              @click="setFilter(status.completed)"
            >
              {{ status.completed }}
            </li>
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
  min-height: 100vh;
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
  height: 300px;
  padding: 10px;
  overflow: auto;
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
  width: 100%;
}

.todo-check:checked + .todo-label::before {
  border: 1px solid rgb(240, 200, 240);
  background: rgb(240, 200, 240) url('https://static-00.iconduck.com/assets.00/tick-01-icon-2048x1653-m70buoup.png')
    center center / 100% no-repeat;
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
  width: 400px;
  padding: 10px;
}

.todo-label::before {
  content: '';
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
  transition: opacity 0.3s ease;
}

.todo-edit:hover,
.todo-remove:hover {
  opacity: 0.8;
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
  transition: opacity 0.3s ease;
}

.todo-add input {
  width: 100%;
  height: 50px;
  background-color: #fff;
  padding: 10px 20px;
}

.todo-add input:focus {
  outline-color: #c15dff;
}

.todo-footing {
  background-color: #ffecef;
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
  transition: color 0.3s ease;
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
