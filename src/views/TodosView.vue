<script setup>
import { ref, watch, computed } from "vue";
import { uid } from "uid";
import TodoCreator from "../components/TodoCreator.vue";
import TodoItem from "../components/TodoItem.vue";
import { Icon } from "@iconify/vue";

const todoList = ref([]);

// Sempre que houver uma mudança no elemento q vc passar no primeiro parametro ele vai executar a função do segundo paramentro
watch(todoList, () => {
  setTodoListLocalStorage()
}, {
  // viabiliza que a função detecte mudanças nas propriedades dos objetos do array
  deep: true,
})


const todoCompleted = computed(() => {
  return todoList.value.every((todo) => todo.isCompleted)
})

const fetchTodoList = () => {
  const savedTodoList = JSON.parse(localStorage.getItem("todoList"))
  if(savedTodoList) {
    todoList.value = savedTodoList
  }
}

fetchTodoList()

const setTodoListLocalStorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value))
}

const createTodo = (todo) => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: null,
    isEditing: null,
  });
}

const toggleEditTodo = (todoPosition) => {
  todoList.value[todoPosition].isEditing = !todoList.value[todoPosition].isEditing;
};

const toggleTodoComplete = (todoPosition) => {
  todoList.value[todoPosition].isCompleted = !todoList.value[todoPosition].isCompleted;
};

const updateTodo = (todoValue, todoPosition) => {
  todoList.value[todoPosition].todo = todoValue
}

const deleteTodo = (todoID) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== todoID)
}
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem
        v-for="(todo, index) in todoList"
        :todo="todo"
        :index="index"
        @toggle-complete="toggleTodoComplete"
        @edit-todo="toggleEditTodo"
        @update-todo="updateTodo"
        @delete-todo="deleteTodo"
      />
    </ul>
    <p class="todos-msg" v-else>
      <Icon icon="noto-v1:sad-but-relieved-face" />
      <span>SEM TAREFAS!</span>
    </p>
    <p v-if="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" />
      <span>TODAS AS TAREFAS COMPLETAS!</span>
    </p>
  </main>
</template>

<style lang="scss" scoped>
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
