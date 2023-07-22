<script setup>
import ToDoCreator from '../components/TodoCreator.vue';
import {uid} from 'uid';
import { ref, computed, watch } from 'vue';
import TodoItem from '../components/TodoItem.vue';
import { Icon } from "@iconify/vue";


const todoList = ref([]);


watch(
  todoList,
  (newTodo, oddTodo) => {
    setTodoListLocalStorage();
  },
  {
    deep: true,
  }
);

const fetchTodoList = () => {
  const savedTodoList = JSON.parse(localStorage.getItem("todoList"));
  if (savedTodoList) {
    todoList.value = savedTodoList;
  }
};

// Fetch Todo's on page load
fetchTodoList();

const setTodoListLocalStorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value));
};
const todosCompleted = computed(()=>{
  return todoList.value.every((todo) => todo.isCompleted);
});

const createTodo = (todo) => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: false,
    isEditing: null,
  });
};

const toggleTodoComplete = (indx) => {
  // console.log(hello);
  todoList.value[indx].isCompleted = !(todoList.value[indx].isCompleted);
}
const toggleEditTodo = (indx) => {
  todoList.value[indx].isEditing = !(todoList.value[indx].isEditing);
}
const updateTodo = (value, indx) => {
  console.log(value);
  console.log(indx);
  todoList.value[indx].todo = value;
}
const deleteTodo = (todo) => {
  todoList.value = todoList.value.filter(
    (todoFilter) => todoFilter.id !== todo.id
  );
}
</script>

<template>
  <main>
    <h1>Create Todo</h1>

    <ToDoCreator @create-todo="createTodo"/>

    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem v-for="(todo, index) in todoList" :todo="todo" :index="index" 
      @toggle-complete="toggleTodoComplete"
      @edit-todo="toggleEditTodo"
      @update-todo="updateTodo"
      @delete-todo="deleteTodo"
      ></TodoItem>
    </ul>
    <p v-else class="todos-msg">
      <Icon icon="noto-v1:sad-but-relieved-face" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p v-if="todosCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" />
      <span>You have completed all your todos!</span>
    </p>
  </main>
</template>

<style scoped lang="scss">
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }
  .todo-list {
    display: flex;
    max-width: 500px;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>
