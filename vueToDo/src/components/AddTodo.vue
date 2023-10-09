<script >

import TodoButton from './TodoButton.vue'
import { uid } from 'uid';

export default {
  name: "AddTodo",

  props: {
    todoLists: Array,
    isItEditing: Boolean,
    editId: String,
    editTask: Object,
    categories: Object
  },

  data() {
    return {
      todo: "",
      color: "black",
      editingId: "",
      isEditing: this.isItEditing,
      categoryChoosen: { ...this.categories },
      newCategory: false,
      error: "",
      addingNewCategoryValue: ""
    }
  },

  components: {
    TodoButton
  },

  methods: {
    //generates key id by uid
    generateKey() {
      return uid();
    },
    // on clicking + child comp will emit to update it's parent component
  
    createEditTodo() {
      // If color task and category all are choosen then and then only allow to add item
      const categoryCheckListArr = Object.values(this.categoryChoosen)
      const isCategoryChoosen = categoryCheckListArr.some(val => val == true)
      //if category is not choosen then give error
      if (!isCategoryChoosen) {
        this.error = "Please Choose Category!!";
        setTimeout(() => {
          this.error = "";
        }, 2000)
        return;
      }

      //if the todo exists
      if (this.todo) {
        const todoInfo = {
          color: this.color,
          todo: this.todo,
          categoryChoosen: { ...this.categoryChoosen },
          editId: this.editingId
        }
        //emit for adding a task
        if (!this.isEditing) {
          this.$emit('createTodoTask', todoInfo);
        }
        //emit for editing an existing task
        else {
          this.$emit('editTodoTask', todoInfo)
        }
        this.todo = "";
      }
      //task should not be empty
      else {
        this.error = "Can't add an Empty task"
        setTimeout(() => {
          this.error = "";
        }, 2000)
      }
    },
    //as new catogry is added empty newCategory and make the new category added as selected 
    handleAddingNewCategory() {
      this.newCategory = false;
      this.categoryChoosen[this.addingNewCategoryValue] = true;
      this.addingNewCategoryValue = ""
    },

    //if selected value of dropdown is add category then make newCategory as true
    handleCategoryChange(e) {
      if (e.target.value == "add category") {
        this.newCategory = true;
      }
      //if the given category is selected then deselect it and if deselected select it
      else { this.categoryChoosen[e.target.value] = !this.categoryChoosen[e.target.value] }
    }
  },
  // below method will continously watch the props getting changed
  watch: {
    //if categories are changing then add new values of categories in categoryChoosen 
    //so that updated values can be seen in dropdown list
    categories(newValues) {
      this.categoryChoosen = { ...newValues }
    },
    //if it's editing then go to the flow of editing
    isItEditing(newEdit) {
      this.isEditing = newEdit;
    },
    //edit task info received from parent to display values in input tags
    editTask(taskToBeEditted) {
      if (taskToBeEditted) {
        this.todo = taskToBeEditted.todo;
        this.color = taskToBeEditted.color;
        this.categoryChoosen = taskToBeEditted.category;
        this.editingId = taskToBeEditted.id
      }
    }
  },
}
</script>


<template>
  <div>
    mention the chronology of methods properties
    <!-- ERROR -->
    <div v-show="error" class="flex justify-center text-lg text-red-600 font-bold">{{ this.error }}</div>
    <div class=" flex mt-4 ">
      <!-- Input text for task -->
      <input type="text" v-model="todo" class="h-10  p-2 pl-10 text-sm mr-6 border-2 rounded-full border-green-600"
        placeholder="Enter your task here" />

      <!-- User selects the color of the task -->
      <p class="font-semibold mt-2">Choose color</p><input class="mt-3 mx-2  w-5 h-5" type="color" v-model="color">
      <!-- displaying category list -->
      <div class="relative" v-if="!newCategory">
        <label for="underline_select" class="sr-only">Underline select</label>
        <select multiple @change="handleCategoryChange" id="underline_select" placeholder="Choose Category"
          class="h-16 mx-4 block py-2.5 px-2 w-full text-sm text-gray-500 bg-transparent  border-2 border-black-200   ">
          <option selected disabled>Choose Category</option>
          <!-- for adding new category to the categories list -->
          <option v-for="(category) in Object.keys(categoryChoosen)" :key="generateKey(category)" :value="category"
            :selected="categoryChoosen[category]">{{ category }}
          </option>
          <option value="add category" class="font-bold  text-sm ">Add New Category +</option>
        </select>
      </div>

      <!-- Chooses new category of the task converts dropdown to input tag -->
      <div class="flex  ml-2 justify-center" v-else>
        <!-- input tag for adding new category -->
        <input v-model="addingNewCategoryValue" type="text" class="mt-2 border-2 rounded-lg mr-2 p-2 h-7 border-black"
          placeholder="Enter your new category">
        <!-- + button to add new category -->
        <TodoButton class="ml-4 mt-0" @click="handleAddingNewCategory">
          <template #addNewCategory>
            +</template>
        </TodoButton>

        <!-- For cancelling the new category input -->
        <TodoButton class="ml-4 mt-0" @click="newCategory = false">
          <template #cancel> X</template>
        </TodoButton>
      </div>

      <!-- For adding new task in todo use Add button -->
      <TodoButton v-if="!isEditing" class="ml-8" @click="createEditTodo">
        <template #add>Add</template>
      </TodoButton>

      <!-- For editing existing task in todo use Edit button -->
      <TodoButton v-else class="ml-8" @click="createEditTodo">
        <template #add>Edit</template>
      </TodoButton>
    </div>

  </div>
</template>
