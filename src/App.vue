<template>
  <NavBar />
  <div class="container">
    <router-view />
  </div>
    <TodoToast />
</template>

<script>
import TodoToast from "@/components/TodoToast";
import NavBar from "@/components/NavBar";

export default {
  components: {
    TodoToast,
    NavBar
  }
}
</script>

<style scoped>

</style>
