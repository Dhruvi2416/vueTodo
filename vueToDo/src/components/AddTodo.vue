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
  components: {
    TodoButton
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
  //here constantly the given props will be computed and as categories are duplicated 
  //hence with lodashlibrary categoryList will be rendered uniquely



  // below method will continously watch the props getting changed
  watch: {

    categories(newValues) {
      this.categoryChoosen = { ...newValues }
    },
    isItEditing(newEdit) {
      this.isEditing = newEdit;
      console.log(newEdit)
    },
    editTask(taskToBeEditted) {
      if (taskToBeEditted) {

        console.log("TASKTOBE", taskToBeEditted)
        this.todo = taskToBeEditted.todo;
        this.color = taskToBeEditted.color;
        this.categoryChoosen = taskToBeEditted.category;
        this.editingId = taskToBeEditted.id
      }
    }
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
      if (!isCategoryChoosen) {
        console.log("CATEGCHOO", this.categoryChoosen)
        console.log("NCCH", this.newCategory)
        this.error = "Please Choose Category!!";
        setTimeout(() => {
          this.error = "";
        }, 2000)
        return;
      }
      //if the todo exists

      if (this.todo) {
        console.log("THIS FOT TODO", this.todo)
        const todoInfo = {
          color: this.color,
          todo: this.todo,
          categoryChoosen: { ...this.categoryChoosen },
          editId: this.editingId
        }
        if (!this.isEditing) {
          this.$emit('createTodoTask', todoInfo);
        }
        else {
          this.$emit('editTodoTask', todoInfo)
        }
        this.todo = "";
      }
      else {
        this.error = "Can't add an Empty task"
        setTimeout(() => {
          this.error = "";
        }, 2000)
      }
    },
    handleAddingNewCategory() {

      this.newCategory = false;
      this.categoryChoosen[this.addingNewCategoryValue] = true;
      this.addingNewCategoryValue = ""
    },

    handleCategoryChange(e) {
      if (e.target.value == "add category") {
        this.newCategory = true;
      }
      else { this.categoryChoosen[e.target.value] = !this.categoryChoosen[e.target.value] }
    }
  }
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

      <div class="relative" v-if="!newCategory">


        <label for="underline_select" class="sr-only">Underline select</label>
        <select multiple @change="handleCategoryChange" id="underline_select" placeholder="Choose Category"
          class=" mx-4 block py-2.5 px-2 w-full text-sm text-gray-500 bg-transparent  border-2 border-black-200   ">
          <option selected disabled>Choose Category</option>
          <!-- for adding new category to the categories list -->
          <option v-for="(category) in Object.keys(categoryChoosen)" :key="generateKey(category)" :value="category"
            :selected="categoryChoosen[category]">{{ category }}
          </option>
          <option value="add category" class="font-bold  text-sm ">Add New Category +</option>
        </select>


      </div>

      <!-- Chooses new category of the task -->
      <div class="flex  ml-2 justify-center" v-if="newCategory">

        <input v-model="addingNewCategoryValue" type="text" class="mt-2 border-2 rounded-lg mr-2 p-2 h-7 border-black"
          placeholder="Enter your new category">

        <TodoButton class="ml-4 mt-0" @click="handleAddingNewCategory">

          <!-- For cancelling the new category input -->
          <template #addNewCategory>

            Y</template>
        </TodoButton>

        <TodoButton class="ml-4 mt-0" @click="newCategory = false">

          <!-- For cancelling the new category input -->
          <template #cancel>

            X</template>
        </TodoButton>
      </div>

      <TodoButton v-if="!isEditing" class="ml-8" @click="createEditTodo">

        <!-- For adding new task in todo use + button -->
        <template #add>

          Add</template>
      </TodoButton>
      <TodoButton v-else class="ml-8" @click="createEditTodo">

        <!-- For adding new task in todo use + button -->
        <template #add>

          Edit</template>
      </TodoButton>
    </div>

  </div>
</template>
