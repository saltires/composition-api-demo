<template>
  <div>
    <section id="app" class="todoapp">
      <header class="header">
        <h1>todos</h1>
        <input
          class="new-todo"
          placeholder="What needs to be done?"
          autocomplete="off"
          autofocus
          v-model="input"
          @keyup.enter="addToDo"
        />
      </header>
      <section class="main">
        <input id="toggle-all" class="toggle-all" type="checkbox" />
        <label for="toggle-all">Mark all as complete</label>
        <ul class="todo-list">
          <li v-for="todo in todos" :key="todo" :class="{completed: todo.completed, editing: todo == editingTodo}">
            <div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed" />
              <label @dblclick="editTodo(todo)">{{ todo.text }}</label>
              <button class="destroy" @click="removeTodo(todo)"></button>
            </div>
            <input class="edit" @keyup.enter="doneEdit(todo)" type="text" @blur="doneEdit(todo)" v-model="todo.text" @keyup.esc="cancelEdit(todo)"/>
          </li>
        </ul>
      </section>
      <footer class="footer">
        <span class="todo-count"> <strong></strong> left </span>
        <ul class="filters">
          <li><a href="#/all">All</a></li>
          <li><a href="#/active">Active</a></li>
          <li><a href="#/completed">Completed</a></li>
        </ul>
        <button class="clear-completed">Clear completed</button>
      </footer>
    </section>
    <footer class="info">
      <p>Double-click to edit a todo</p>
      <!-- Remove the below line ↓ -->
      <p>Template by <a href="http://sindresorhus.com">Sindre Sorhus</a></p>
      <!-- Change this out with your name and url ↓ -->
      <p>Created by <a href="https://www.lagou.com">教瘦</a></p>
      <p>Part of <a href="http://todomvc.com">TodoMVC</a></p>
    </footer>
  </div>
</template>

<script>
import './assets/index.css';
import { ref } from 'vue';

// 添加待办事项
const useAddTodo = (todos) => {
  const input = ref('');
  const addToDo = () => {
    const text = input.value && input.value.trim();
    if (text.length === 0) return;
    todos.value.unshift({
      text: input.value,
      completed: false,
    });
    input.value = '';
  };

  return {
    input,
    addToDo,
  };
};

// 删除待办事项
const useRemove = (todos) => {
  const removeTodo = (todo) => {
    const index = todos.value.indexOf(todo);
    todos.value.splice(index, 1);
  };

  return {
    removeTodo,
  };
};

// 编辑待办事项
const useEdit = (remove) => {
  let beforeEditText = '';
  const editingTodo = ref(null);

  const editTodo = (todo) => {
    beforeEditText = todo.text;
    editingTodo.value = todo;
  };

  const doneEdit = (todo) => {
    if (!editingTodo.value) return;
    todo.text = todo.text.trim();
    todo.text || remove(todo);
    editingTodo.value = null;
  };

  const cancelEdit = (todo) => {
    editingTodo.value = null;
    todo.text = beforeEditText;
  };

  return {
    editTodo,
    doneEdit,
    cancelEdit,
    editingTodo
  }
};

export default {
  name: 'App',
  setup() {
    const todos = ref([]);
    const { remove } = useRemove(todos);
    return {
      todos,
      ...useAddTodo(todos),
      ...useEdit(remove),
    };
  },
};
</script>
