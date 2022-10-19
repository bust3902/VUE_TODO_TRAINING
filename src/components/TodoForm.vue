<template>
  <div v-if="loading">
  Loading ..
  </div>
  <form v-else @submit.prevent="onSave">
  <div class="row">
    <div class="col-6">
<!--      <div class="form-group">
        <label>Subject</label>
        <input v-model="todo.subject" type="text" class="form-control">
        <div v-if="subjectError" class="text-red">{{ subjectError }}</div>
      </div>-->
      <Input label="Subject" v-model:subject="todo.subject" :error="subjectError" />
    </div>
    <div v-if="editing" class="col-6">
      <div class="form-group">
        <label>Status</label>
        <div>
          <button type="button" class="btn"
                  :class="todo.completed ? 'btn-success' : 'btn-danger'" @click="toggleTodoStatus">
            {{todo.completed ? 'completed' : 'Incompleted' }}
          </button>
        </div>
      </div>
    </div>
    <div class="col-12">
      <div class="form-group">
        <label>Body</label>
        <textarea v-model="todo.body" class="form-control" id="" cols="30" rows="10"></textarea>
      </div>
    </div>
  </div>
  <button type="submit" class="btn btn-primary" :disabled="!todoUpdated">{{ editing ? 'Update' : 'Create' }}</button>
  <button class="btn btn-outline-dark ms-2" @click="moveToTodoListPage">Cancel</button>
  </form>
</template>

<script>

import { useRoute, useRouter } from 'vue-router'
import { useToast } from "@/composables/toast";
import { ref, computed, onUpdated } from "vue";
import axios from '@/axios';
import _ from 'lodash';
import Input from '@/components/TodoInput';

export default {
  components: {
    Input
  },
  props: {
    editing: {
      type: Boolean,
      default: false
    }
  },
  setup(props) {

    const route = useRoute();
    const router = useRouter();
    const todo = ref({
      subject: '',
      completed: false,
      body: ''
    });

    onUpdated(() => {
      console.log(todo.value.subject)
    })

    const subjectError = ref('');
    const originalTodo = ref(null);
    const loading = ref(false);

    const todoId = route.params.id;

    const {
      showToast,
      toastMessage,
      toastAlertType,
      triggerToast
    } = useToast();
    // const showToast = ref(false);
    // const toastMessage = ref('');
    // const toastAlertType = ref('');
    // const timeout = ref(null);
    // const triggerToast = (message, type = 'success') => {
    //   toastMessage.value = message;
    //   toastAlertType.value = type;
    //   showToast.value = true;
    //   timeout.value = setTimeout(() => {
    //     toastMessage.value = '';
    //     toastAlertType.value = '';
    //     showToast.value = false;
    //   }, 3000)
    // }

    const getTodo = () => {
      loading.value = true;
      axios.get(`todos/${todoId}`, {
        //axios.get('http://localhost:3000/todos/' + todoId, {      // 위와 동일한 기능의 코드

      }).then(response => {
        todo.value = { ...response.data };
        originalTodo.value = { ...response.data };
        loading.value = false;
      }).catch(err => {
        loading.value = false;
        console.log(err)
        triggerToast('Something went wring', 'danger');
      })
    };

    const todoUpdated = computed(() => {
      return !_.isEqual(todo.value, originalTodo.value);
    })

    const toggleTodoStatus = () => {
      todo.value.completed = !todo.value.completed;
    };

    const moveToTodoListPage = () => {
      router.push({
        name: 'Todos'
      })
    };

    const onSave = async () => {
      subjectError.value = '';
      if (!todo.value.subject) {
        subjectError.value = 'Subject is required';
        return;
      }

      try {
        let response;
        const data = {
          subject: todo.value.subject,
          completed: todo.value.completed,
          body: todo.value.body
        }
        if (props.editing) {
          response = await axios.put(`todos/${todoId}`, data);
          originalTodo.value = {...response.data};
        } else {
          response = await axios.post('todos', data);
          todo.value.subject = '';
          todo.value.body = '';
        }
        const message = 'Successfully ' + (props.editing ?  'updated!' : 'Created!');
        triggerToast(message);

        if (!props.editing) {
          router.push({
            name: 'Todos'
          })
        }
      } catch {
        triggerToast('Something went wring', 'danger');
      }
    };

    if (props.editing) {
      getTodo();
    }

    return {
      todo,
      loading,
      toggleTodoStatus,
      moveToTodoListPage,
      onSave,
      todoUpdated,
      showToast,
      toastMessage,
      toastAlertType,
      subjectError,
    };
  }
}
</script>

<style scoped>

  .fade-enter-active,
  .fade-leave-active {
    transition: all 0.5s ease;
  }

  .fade-enter-from,
  .fade-leave-to {
    opacity: 0;
    transform: translateY(-30px);
  }

  .fade-enter-to,
  .fade-leave-from {
    opacity: 1;
    transform: translateY(0px);
  }
</style>