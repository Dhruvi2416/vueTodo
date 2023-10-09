<script>
import TodoButton from './TodoButton.vue';

export default {
    name: "SearchFilteredList",
    data() {
        return {
            searchTask: "",
            searchColor: "Search Color",
            searchCategory: "Search Category"
        };
    },
    components: { TodoButton },
    methods:{
        emitSearchFiltering(){
            console.log(this.searchTask)
            this.$emit('emitFilteredList',this.searchTask)
        }
    }
}
</script>

<template>
    <div class="flex">
        <input type="text" placeholder="Search Task" v-model="searchTask" class="border-2  pl-2">
        <label for="underline_select" class="sr-only">Underline select</label>
        <select  id="underline_select" v-model="searchColor"
            class=" mx-4 block py-2.5 px-2 w-full text-sm text-gray-500 bg-transparent  border-2 border-black-200   ">
           
            <option selected disabled>Search Color</option>
        </select>
        <select  id="underline_select" v-model="searchCategory"
            class=" mx-4 block py-2.5 px-2 w-full text-sm text-gray-500 bg-transparent  border-2 border-black-200   ">
           
            <option selected disabled>Search Category</option>
        </select>
        <TodoButton @click="emitSearchFiltering">
            <template #apply>Apply</template>
        </TodoButton>
    </div>
</template>


