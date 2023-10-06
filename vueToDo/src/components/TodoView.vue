<script>

import AddTodo from './AddTodo.vue';
import DisplayHideDoneTasks from './DisplayHideDoneTasks.vue';
import TodoButton from './TodoButton.vue';
import { uid } from 'uid';

export default {
    name: "TodoView",
    data() {

        return {

            todoList: [],
            id: 0,
            editMsg: "",
            hideCompleted: false,
            editColor: "black",
            editCategory: "Choose Category"
        }
    },
    //this will execute at the time of initialization
    mounted() {
        const storedTaskList = localStorage.getItem("taskList");
        if (storedTaskList) { this.todoList = JSON.parse(storedTaskList) }


    },
    components: { AddTodo, TodoButton, DisplayHideDoneTasks },
    methods: {

        // whatever the user add tasks below function will add it in array of todolist
        handleTodoLists(todoInfo) {


            const newTask = {
                todo: todoInfo.todo,
                id: uid(),
                isCompleted: false,
                color: todoInfo.color,
                category: todoInfo.categoryChoosen
            }
            //newtask is added in old todolist
            this.todoList = [...this.todoList, newTask]
            // newly updated todolist set in localstorage
            localStorage.setItem("taskList", JSON.stringify(this.todoList));
        },


        // this below function will first find the edited task with id then pass the edit message to child component and 
        // will be removed from the list
        handleEditing(editTask) {
            // ------blahesblahlint-diblahsable-neblahxt-lblahine------ remove blah and this line will disable next line
            // debugger
          
            this.todoList = this.todoList.filter((task) => task.id != editTask.id)
            this.editMsg = editTask.todo
            this.editCategory = editTask.category
            this.editColor = editTask.color

           
            return this.editMsg
        },
        //deleting todo task
        handleDelete(deleteTaskId) {
            const alert = window.confirm("Are you sure you want to delete?")
            if (alert) {
                this.todoList = this.todoList.filter((task) => task.id != deleteTaskId)
                localStorage.setItem("taskList", JSON.stringify(this.todoList));
            }
        },

        //if the task is done/undone then it will change to undone/done and will update that property of 
        //isCompleted and will set it in localstorage
        handleDoneTasks(task) {
            task.isCompleted = !task.isCompleted;
            this.todoList.find(todo => { if (todo.id == task.id) { todo.isCompleted = task.isCompleted } })
            localStorage.setItem("taskList", JSON.stringify(this.todoList));
        }
    },
    //this willcontinously check on the changes of the states which below function depends on
    // if showAll then display all tasks and if not showAll then show only pending tasks
    computed: {
        displayList() {
            return !this.hideCompleted ? this.todoList : this.todoList.filter(task => task.isCompleted == false)
        }
    },



}



</script>

<template>
    <div class=" border-2 p-4 border-green-600 rounded-lg ">

        <nav class="flex justify-center align-middle border-b-2 border-green-600">
            <h1 class="text-3xl font-bold text-green-600 mb-4"> Vue - ToDo App </h1>
        </nav>
        <!-- checkbox for selecting either to showAll tasks or onlu undone tasks -->
        <div class="flex justify-end mt-2 ">

            <DisplayHideDoneTasks :hide="this.hideCompleted" @emitShow="this.hideCompleted = !this.hideCompleted" />
        </div>
        <!-- Input tag child component with dynamically passing editted msg props -->
        <AddTodo @createTodoTask="handleTodoLists" :msg="editMsg" :editColor="editColor" :editCategory="editCategory" />

        <!-- list of todos -->

        <!-- iterates displayList based on showAll property -->
        <ul v-for="(task) in displayList" :key="task.id">
            <div class=" flex my-2 border-2 p-2 rounded-lg ">
                <div :class="{ 'mr-auto': true, 'line-through': task.isCompleted }">
                    <!-- onClicking taskcompleted or not can be checked -->
                    <h1 class="text-lg font-bold">{{ task.category }}</h1>
                    <!-- task will be added with given colors -->
                    <p @click="() => handleDoneTasks(task)" :style="{ color: task.color }">{{
                        task.todo }}</p>
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



