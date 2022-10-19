<template>
  <nav class="navbar navbar-expand-lg bg-light">
    <router-link class="navbar-brand" to="/">Training</router-link>
    <!--
      routerIndex.js에 설정한 name을 이용하여 위와 같이 사용할 수 있다.
      <router-link class="navbar-brand" :to="{ name: 'home' }">Training</router-link>
    -->
    <ul class="navbar-nav me-auto mb-2 mb-lg-0">
      <li class="nav-item">
        <router-link class="nav-link" to="/todos">Todos</router-link>
      </li>
    </ul>
  </nav>
</template>

<script>
export default  {

}
</script>

<style>

</style>