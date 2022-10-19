<template>
  <div>
    <div class="d-flex justify-content-between mb-3">
      <h2>To-Do List</h2>
      <button class="btn btn-primary" @click="moveToCreatePage">Create Todo</button>
    </div>

    <input class="form-control" type="text" v-model="searchText" placeholder="search" @keyup.enter="searchTodo">
    <hr />
    <div style="color : red">{{ error }}</div>
    <div v-if="!todos.length">
      There is nothing to display
    </div>
    <TodoList :todos="todos" @toggle-todo="toggleTodo" @delete-todo="deleteTodo"/>
    <hr />
  <Pagination v-if="todos.length" :numberOfPages="numberOfPages" :currentPage="currentPage" @click="getTodos" />
  </div>
</template>

<script>
import { ref, computed, watch } from 'vue';
import TodoList from "@/components/TodoList.vue";
import axios from '@/axios';
import { useToast } from "@/composables/toast";
import { useRouter } from 'vue-router';
import Pagination from "@/components/TodoPagination";


export default {
  components: {
    TodoList,
    Pagination
  },
  setup() {
    const router = useRouter();
    const todos = ref([]);
    const error = ref('');
    const numberOfTodos = ref(0);
    const limit = 5;
    const currentPage = ref(1);
    const searchText = ref('');

    const {
      showToast,
      toastMessage,
      toastAlertType,
      triggerToast
    } = useToast();

    const numberOfPages = computed(() => {
      return Math.ceil(numberOfTodos.value/limit);
    })
    // const a = reactive({
    //   b:1
    // })
    // watchEffect(() => {
    //   console.log(a.b);
    // });
    // a.b = 4;

    const getTodos = (page = currentPage.value) => {
      currentPage.value = page;
      axios.get(`todos?_sort=id&_order=desc&subject_like=${searchText.value}&_page=${page}&_limit=${limit}`, {

      }).then(response => {
        numberOfTodos.value = response.headers['x-total-count'];
        todos.value = response.data;
      }).catch(err => {
        console.log(err);
        triggerToast('Something went wring', 'danger');
      })
    }

    getTodos();

    const addTodo = async (todo) => {
      // DB에 Todo를 저장 후 Array(const todos)에 저장
      error.value = '';
      try {
        await axios.post('http://localhost:3000/todos', {
          subject: todo.subject,
          completed: todo.completed
        });

        getTodos(1);
      } catch(err) {
        console.log(err);
        triggerToast('Something went wring', 'danger');
      }
      //       .then(response =>{
      //     console.log(response);
      //     todos.value.push(response.data);
      //   }).catch(err => {
      //     console.log(err);
      //     error.value = 'Something went wrong.';
      //   })
      console.log('hello')
    };

    const deleteTodo = (id) => {
      error.value = '';
      axios.delete('todos/' + id, {

      }).then(response =>{
        console.log(response);
        getTodos(1);
      }).catch(err => {
        console.log(err);
        triggerToast('Something went wring', 'danger');
      })
    };
    const toggleTodo = (index, checked) => {
      error.value = '';
      const id = todos.value[index].id;
      axios.patch('todos/' + id, {
        completed: checked
      }).then(response =>{
        console.log(response);
        todos.value[index].completed = checked
      }).catch(err => {
        console.log(err);
        triggerToast('Something went wring', 'danger');
      })
    };

    // const toggleTodo = (index) => {
    //   console.log(todos.value[index]);
    //   todos.value[index].completed = !todos.value[index].completed
    //   console.log(todos.value[index]);
    // };

    let timeout = null;
    const searchTodo = () => {
      clearTimeout(timeout);
      getTodos(1);
    };

    watch(searchText, () => {
      clearTimeout(timeout);
      timeout = setTimeout(() => {
        getTodos(1);
      }, 2000)
    });

    // const filteredTodos = computed(() => {
    //   if (searchText.value) {
    //     return todos.value.filter(todo => {
    //       return todo.subject.includes(searchText.value);
    //     });
    //   }
    //   return todos.value;
    // })

    const moveToCreatePage = () => {
      router.push ({
        name: 'TodoCreate',
      })
    }

    return {
      todos,
      addTodo,
      deleteTodo,
      toggleTodo,
      searchText,
      // filteredTodos,
      error,
      numberOfPages,
      currentPage,
      getTodos,
      searchTodo,
      showToast,
      toastMessage,
      toastAlertType,
      moveToCreatePage
    };
  }
}
</script>

<style>
.todo {
  color: gray;
  text-decoration: line-through;
}

</style>
