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
  <header class=" ">
    <div class=" flex justify-center self-center mt-12  ">
      <TodoView />
    </div>
  </header>
</template>

