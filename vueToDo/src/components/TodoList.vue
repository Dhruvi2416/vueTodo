<script>
import TodoButton from './TodoButton.vue'
export default {

name:"TodoList",

    data() {
        return {
            todoList: [],
            editMsg: "",

        };
    },
components:{
    TodoButton
},
    props: {
        list: Array,
        showAll: Boolean
    },
    created() {
        this.todoList = [...this.list]
    },

    methods: {


        // this below function will first find the edited task with id then pass the edit message to child component and 
        // will be removed from the list
        handleEditing(editTask) {
            const newTodo = this.todoList.filter((task) => task.id == editTask.id);
            this.editMsg = newTodo[0].todo;
            this.todoList = this.todoList.filter((task) => task.id != editTask.id);
            this.$emit('editTask', this.editMsg);
            return this.editMsg;
        },
        //deleting todo task
        handleDelete(deleteTaskId) {
            this.todoList = this.todoList.filter((task) => task.id != deleteTaskId);
        },
    },
    computed: {
        filteredList() {
            return this.showAll ? this.todoList : this.todoList.filter(task => task.isCompleted == false);
        }
    },

}
</script>


<template>
    <div>
    
        <ul v-for="(task) in filteredList" :key="task.id">
            
            <div class=" flex my-2 border-b-2 py-2 ">
                <div :class="{ 'mr-auto': true, 'line-through': task.isCompleted }">
                    <!-- onClicking taskcompleted or not can be checked -->
                    <p @click="task.isCompleted = !task.isCompleted">{{ task.todo }}</p>
                </div>
                <div class=" mr-8 ">

                    <!-- edit buton -->
                    <TodoButton @click="() => handleEditing(task)"><template #editTodo><i class="fa fa-pencil"></i>
                        </template></TodoButton>
                </div>
                <div class="">

                    <!-- delete button -->
                    <TodoButton @click="() => handleDelete(task.id)"><template #deleteTodo><i class="fa fa-trash"></i>
                        </template></TodoButton>
                </div>
            </div>
        </ul>
    </div>
</template>

