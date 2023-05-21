<script setup>
import { ref , watch , computed} from 'vue';
import { uid } from 'uid';
import { Icon } from '@iconify/vue';
import ToDoCreator from '../components/ToDoCreator.vue';
import ToDoItem from '../components/ToDoItem.vue';
const todoList = ref([]);

watch (
todoList,
()=>{
  setTodoListLocalStorage();
},
{
  deep: true,
}
);

const todoCompleted = computed(() => {
  return todoList.value.every((todo) => todo.isCompleted);
})

const fetchTodoList = ()=>{
  const savedTodoList = JSON.parse(localStorage.getItem("todoList"));
  if (savedTodoList){
    todoList.value = savedTodoList;
  }
};

fetchTodoList();

const setTodoListLocalStorage = () => {
localStorage.setItem("todoList", JSON.stringify(todoList.value));
};


const createTodo = (todo) =>{
  todoList.value.push({
    id:uid(),
    todo,
    isCompleted:null,
    isEditing:null,
  });
};
const onToggleComplete = (todoPos) =>{
  todoList.value[todoPos].isCompleted = !todoList.value[todoPos].isCompleted;
};

const onToggleEditTodo = (todoPos) =>{
  todoList.value[todoPos].isEditing = !todoList.value[todoPos].isEditing;
};

const updateTodo = (todoVal,todoPos) =>{
  todoList.value[todoPos].todo = todoVal;
};
const deleteTodo = (todoId) =>{
  todoList.value = todoList.value.filter((todo)=>todo.id !== todoId)
};


</script>

<template>
  <main>
    <h1>Create ToDo</h1>
    <ToDoCreator @create-todo="createTodo"/>

    <ul class="todo-list"  v-if="todoList.length > 0">
      <ToDoItem v-for="(todo, index) in todoList"
       :todo = "todo" 
       :index= "index"
       @toggle-complete="onToggleComplete"
       @edit-todo="onToggleEditTodo"
       @update-todo="updateTodo"
       @delete-todo="deleteTodo" />
    </ul>

    <span class="todos-msg" v-else>    
      <Icon icon="bi:emoji-smile" color="green" />
      <p>You have no todo's to complete. Add one!</p>

  </span>

  <p v-if="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" />
      <span><p>You have completed all your todos!</p></span>
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
