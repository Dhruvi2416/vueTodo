<script>

import AddTodo from './AddTodo.vue';
import DisplayHideDoneTasks from './DisplayHideDoneTasks.vue';
import TodoButton from './TodoButton.vue';
import { uid } from 'uid';
import _ from 'lodash';
import SearchFilteredList from './SearchFilteredList.vue';
import DisplayTodoList from './DisplayTodoList.vue';

export default {
    name: "TodoView",
    data() {
        return {
            todoList: [],
            hideCompleted: false,
            isEditing: false,
            editId: "",
            isFiltering: false,
            filterTask: {
                text: "",
                color: "",
                categories: {}
            },
        }
    },

    //this willcontinously check on the changes of the states which below function depends on
    // if showAll then display all tasks and if not showAll then show only pending tasks
    computed: {
        displayList() {
            if (this.isFiltering) {
                let filteredList = this.todoList.filter(task => task.todo.includes(this.filterTask.text) && task.color.includes(this.filterTask.color))
                if (Object.values(this.filterTask.categories).some(value => value == true)) { filteredList = this.filterCategories(filteredList); }

                return !this.hideCompleted ? filteredList : filteredList.filter(task => task.isCompleted == false)
            }
            return !this.hideCompleted ? this.todoList : this.todoList.filter(task => task.isCompleted == false)
        },

        fetchTaskFromId() {
            if (this.editId) {
                const editingTask = this.todoList.find(todo => todo.id == this.editId)
                return editingTask;
            }

        },
        fetchCategories() {
            const categoryList = this.todoList.map(todo => todo.category);
            //  return _.uniq(categoryList);
            if (categoryList.length > 0) {
                const arr = [];
                categoryList.map(cat => arr.push(...Object.keys(cat)))
                const obj = {};
                const uniqueArr = _.uniq(arr);
                uniqueArr.map(category => obj[category] = false)
                return obj;
            }
            else {
                return {}
            }
        },

        fetchColors() {
            const colors = this.todoList.map(todo => todo.color);
            const uniqueColors = _.uniq(colors);
            return uniqueColors;
        },

    }, components: { AddTodo, TodoButton, DisplayHideDoneTasks, SearchFilteredList, DisplayTodoList },
    //this will execute at the time of initialization


    methods: {
        filterCategories(taskList) {
            //tasklist ekle filter thaine aveli list color ne text wali
            if (taskList) {
                const filteredList = [];
                for (const task of taskList) {
                    let matchesFilter = false;
                    //filter task ekle ke je object ayo filter na apply thi ee
                    Object.keys(this.filterTask.categories).forEach(category => {
                        if (this.filterTask.categories[category] && task.category[category]) {
                            matchesFilter = true;
                        }
                    })
                    if (matchesFilter) {
                        filteredList.push(task);
                    }
                }
                return filteredList;
            }
        },

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
            this.isFiltering = false;
            this.editId = editTask.id;
            this.isEditing = true;
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
        },

        //save editing task here whenever the editTask is emitted editTask will be come again 
        saveEditedTask(editTask) {
            const editingTask = this.todoList.find(todo => todo.id == editTask.editId);
            editingTask.todo = editTask.todo;
            editingTask.color = editTask.color;
            editingTask.category = editTask.categoryChoosen;
            localStorage.setItem("taskList", JSON.stringify(this.todoList));
            this.isEditing = false;
            this.editId = "";
        },

        //if on applying filter the values in object form is received thanstroe it in a filtertask state
        assignFilteredValues(filterObject) {
            if (filterObject) {
                this.filterTask = filterObject;
            }
        }
    },

    mounted() {
        const storedTaskList = localStorage.getItem("taskList");
        if (storedTaskList) { this.todoList = JSON.parse(storedTaskList) }
    },
}

</script>

<template>
    <div class="h-screen  overflow-y-auto border-2 p-4 mb-4  bg-blue-900/25  rounded-lg ">

        <nav class="flex justify-center border-b-2  ">
            <h1 class="text-3xl font-bold text-pink-600 mb-4 "> Vue - ToDo App </h1>
        </nav>

        <!-- checkbox for selecting either to showAll tasks or only undone tasks -->
        <div class="flex justify-end mt-2 ">
            <div class="mt-2">
                <DisplayHideDoneTasks :hide="this.hideCompleted" @emitShow="this.hideCompleted = !this.hideCompleted" />
            </div>
            <p v-show="!isFiltering" class="mt-1 mx-2">|</p>

            <!-- Filter Button -->
            <TodoButton v-show="!isFiltering" @click="this.isFiltering = true">
                <template #filter>Filter</template>
            </TodoButton>
        </div>

        <!-- Input tag child component with dynamically passing props renderes only if isFiltering is false -->
        <div v-show="!isFiltering" class="mt-8">
            <AddTodo @createTodoTask="handleTodoLists" @editTodoTask="saveEditedTask" :todoLists="todoList"
                :isItEditing="isEditing" :editId="editId" :editTask="fetchTaskFromId" :categories="fetchCategories" />
        </div>

        <!-------Search Filter todo renders only if isFiltering is true ----->
        <div v-show="isFiltering" class="flex">
            <SearchFilteredList @emitFilteredList="assignFilteredValues" :colorsList="fetchColors" class="mt-4"
                :categoryList="fetchCategories">
            </SearchFilteredList>
            <TodoButton @click="this.isFiltering = false" class="mt-4 px-4">
                <template #filter >X</template>
            </TodoButton>
        </div>

        <!-- list of todos rendereing -->
        <DisplayTodoList :displayList="displayList" @editTask="handleEditing" @deleteTask="handleDelete"
            @isCompletedTask="handleDoneTasks"></DisplayTodoList>

    </div>
</template>



