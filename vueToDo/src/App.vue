<script >

import TodoView from './components/TodoView.vue';


export default {
  name: "App",
  components: {
    TodoView,
  }
}
</script>


<template>
  <header >
    <div class="flex w-full justify-center align-middle mt-12 ">   
      <TodoView />
    </div>
  </header>
</template>


<style></style>
