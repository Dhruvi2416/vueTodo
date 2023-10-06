<script >

import TodoButton from './TodoButton.vue'
import { uid } from 'uid';
export default {
  name: "AddTodo",
  props: {
    msg: String,
    editColor: String,
    editCategory: String
  },
  components: {
    TodoButton
  },
  data() {
    return {
      todo: this.msg,
      color: this.editColor,
      opendropdown: false,
      categories: [],
      categoryChoosen: this.editCategory,
      newCategory: "",
      error: ""

    }
  },
  mounted() {
    const categoriesList = localStorage.getItem("categoriesList");
    if (categoriesList) {
      this.categories = JSON.parse(categoriesList);
    }
    console.log("cate", categoriesList)
  },
  // below method will continously watch the props getting changed
  watch: {
    msg(newEditMsg) {
      this.todo = newEditMsg
    },
    editColor(newEditColor) {
      this.color = newEditColor
    },
    editCategory(newEditCategory) {
      this.categoryChoosen = newEditCategory
    }
  },

  methods: {
    //generates key id by uid
    generateKey(){
      return uid();
    },
    // on clicking + child comp will emit to update it's parent component
    createTodo() {

      // If color task and category all are choosen then and then only allow to add item
      if (this.categoryChoosen == "Choose Category" || this.categoryChoosen == "Add New Category +" || !this.categoryChoosen) {
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
          categoryChoosen: this.categoryChoosen
        }
      
        this.$emit('createTodoTask', todoInfo);
        this.todo = "";
      }
      else {
        this.error = "Can't add an Empty task"
        setTimeout(() => {
          this.error = "";
        }, 2000)
      }
    },

    handleNewCategoryAddition() {
      // if category input box is empty then it wil show an error
      if (!this.newCategory) {
        this.error = "Can't add an empty category!!"
        setTimeout(() => {
          this.error = "";
        }, 2000)
        return;
      }

      // checks whether the new category added is already existing or not
      const isExistingCategory = this.categories.some(category => category.toLowerCase() == this.newCategory.toLowerCase())

      // if category is not existing then it will contert it to lowercase and add in an array
      if (!isExistingCategory) {
        this.categories.push(this.newCategory.toLowerCase());
        this.categoryChoosen = this.newCategory;
        this.newCategory = "";
        localStorage.setItem("categoriesList", JSON.stringify(this.categories));
        console.log("Cate2", this.categories)

      }
      // if category exists then it will show an error
      else {  
        this.error = "Category already exists!!!"
        setTimeout(() => {
          this.error = "";
        }, 2000)
      }
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
      <!-- Chooses new category of the task -->
      <div class="relative">


        <label for="underline_select" class="sr-only">Underline select</label>
        <select v-model="categoryChoosen" id="underline_select" placeholder="Choose Category"
          class=" mx-4 block py-2.5 px-2 w-full text-sm text-gray-500 bg-transparent  border-2 border-black-200   ">
          <option selected disabled>Choose Category</option>
          <!-- for adding new category to the categories list -->
          <option v-for="(category) in categories" :key="generateKey(category)" :value="category">{{ category }}</option>
          <option class="font-bold  text-sm ">Add New Category +</option>
        </select>


      </div>









      <TodoButton class="ml-8" @click="createTodo">

        <!-- For adding new task in todo use + button -->
        <template #add>

          Add</template>
      </TodoButton>
    </div>
    <div class="flex mt-4 justify-center" v-if="categoryChoosen == 'Add New Category +'">
      <input v-model="newCategory" type="text" class="border-2 rounded-lg mr-2 p-2 h-7 border-black"
        placeholder="Enter your new category">
      <TodoButton class="ml-4" @click="handleNewCategoryAddition">

        <!-- For adding new category  -->
        <template #add>

          Add</template>
      </TodoButton>

      <TodoButton class="ml-4" @click="categoryChoosen = 'Choose Category'">

        <!-- For cancelling the new category input -->
        <template #cancel>

          X</template>
      </TodoButton>
    </div>
  </div>
</template>
