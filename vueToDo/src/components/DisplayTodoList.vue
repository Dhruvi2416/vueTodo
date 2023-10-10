<script>
import TodoButton from './TodoButton.vue'
export default {

    name: "DisplayTodoList",
    props: {
        displayList: Array
    },
    data() {
        return {
            listOfTodos: this.displayList
        }
    },

    components: {
        TodoButton
    },

    watch: {
        displayList(newList) {
            this.listOfTodos = newList
        }
    }



}
</script>


<template>
    <div>
        <ul v-for="(task) in listOfTodos" :key="task.id">
            <div class=" flex my-2 border-b-2 border-t-2 py-2 mt-12">
                <div :class="{ 'mr-auto': true }">
                    <!-- onClicking taskcompleted or not can be checked -->
                    <h1 class="text-lg font-bold" :class="{ 'line-through': task.isCompleted }"
                        :style="{ color: task.color }" @click="this.$emit('isCompletedTask', task)">{{ task.todo }}</h1>
                    <!-- displaying categories of task -->
                    <p class="'line-through': false">{{
                        Object.keys(task.category).filter(key => task.category[key]).join(",") }}</p>
                </div>

                <div class=" mr-8 my-2 ">
                    <!-- edit buton -->
                    <TodoButton @click="this.$emit('editTask', task)"><template #editTodo><i class="fa fa-pencil"></i>
                        </template></TodoButton>
                </div>

                <div class=" mr-8 my-2 ">
                    <!-- delete button -->
                    <TodoButton @click="this.$emit('deleteTask', task.id)"><template #deleteTodo><i class="fa fa-trash"></i>
                        </template></TodoButton>
                </div>
            </div>
        </ul>
    </div>
</template>



   