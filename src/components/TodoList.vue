<template>
<!--  <div v-for="(todo, index) in todos" :key="todo.id" class="card mt-2">-->
  <MainList :items="todos">
    <template #default="{ item, index }">
      <div class="card=body p-2 d-flex align-items-center" @click="moveToPage(item.id)" style="cursor: pointer">
        <div class="formCheck flex-grow-1">
          <input class="mx-2" type="checkbox" :checked="item.completed"
                 @change="toggleTodo(index, $event)" @click.stop>
          <span :class="{ todo : item.completed }">{{ item.subject }}</span>
        </div>
        <div>
          <button class="btn btn-danger btn-sm" @click.stop="openModal(item.id)">Delete</button>
        </div>
      </div>
    </template>
  </MainList>
  <teleport to="#modal">
    <Modal v-if="showModal" @close="closeModal" @delete="deleteTodo" />
  </teleport>
</template>

<script>
import { useRouter } from 'vue-router'
import {getCurrentInstance, ref} from 'vue';
import Modal from '@/components/DeleteModal'
import MainList from '@/components/MainList'

export default {
  components: {
    Modal,
    MainList
  },
  props: {
    todos: {
      type: Array,
      required: true
    }
  },
  emits: ['toggle-todo', 'delete-todo'],

  setup() {
    const { emit } = getCurrentInstance();

    const router = useRouter();
    const showModal = ref(false);
    const todoDeleteId = ref(null);
    const toggleTodo = (index, event) => {
      emit('toggle-todo', index, event.target.checked);
    };

    const openModal = (id) => {
      todoDeleteId.value = id;
      showModal.value = true;
    }

    const closeModal = () => {
      todoDeleteId.value = null;
      showModal.value = false;
    }

    const deleteTodo = () => {
      emit('delete-todo', todoDeleteId.value);

      showModal.value = false;
      todoDeleteId.value = null;
    };

    const moveToPage = (todoId) => {
      console.log(todoId);
      // router.push('/todos/' + todoId);
      // 주석의 코드와 하단의 코드는 동일하게 작동한다. routerIndex.js의 path에서 파라미터, name에서 name값을 확인할 수 있다.
      router.push({
        name:'Todo',
        params: {
          id: todoId
        }
      });

    };

    return {
      toggleTodo,
      deleteTodo,
      moveToPage,
      showModal,
      openModal,
      closeModal
    }
  }
}
</script>

<style>

</style>