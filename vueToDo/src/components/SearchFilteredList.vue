<script>
import { uid } from 'uid';
import TodoButton from './TodoButton.vue';

export default {
    name: "SearchFilteredList",
    props: {
        colorsList: Array,
        categoryList: Object
    },
    data() {
        return {
            searchTask: "",
            searchColor: "Search Color",
            searchCategory: { ...this.categoryList }
        };
    },

    components: { TodoButton },
    methods: {
        generateKey() {
            return uid();
        },
        emitSearchFiltering() {

            if (this.searchColor == "Search Color") {
                this.searchColor = "";
            }
            const searchObj = {
                text: this.searchTask,
                color: this.searchColor,
                categories: { ...this.searchCategory }

            }
            this.$emit('emitFilteredList', searchObj)

            if (this.searchColor == "") {
                this.searchColor = "Search Color";
            }



        },
        handleSelectCategory(e) {

            //if the given category is selected then deselect it and if deselected select it
            this.searchCategory[e.target.value] = !this.searchCategory[e.target.value]
        }
    },
    watch: {
        categoryList(newValues) {
            this.searchCategory = { ...newValues };
        }
    }
}
</script>

<template>
    <div class="flex h-10">
        <input type="text" placeholder="Search Task" v-model="searchTask" class="border-2  pl-2">
        <label for="underline_select" class="sr-only">Underline select</label>
        <select id="underline_select" v-model="searchColor"
            class=" mx-4 block py-2.5 px-2 w-full text-sm text-gray-500 bg-transparent  border-2 border-black-200   ">

            <option selected disabled>Search Color</option>
            <option v-for="color in colorsList" :key="generateKey(color)" class="text-white"
                :style="{ backgroundColor: color }">{{ color }}</option>
        </select>
        <select @change="handleSelectCategory" multiple id="underline_select "
            class=" mx-4 block py-2.5 px-2 w-full text-sm text-gray-500 bg-transparent  border-2 border-black-200 h-12   ">

            <option selected disabled>Search Category</option>
            <option :selected="searchCategory[category]" v-for="(category) in Object.keys(categoryList)"
                :key="generateKey(category)">{{ category }}</option>
        </select>
        <div class="flex ">
            <TodoButton @click="emitSearchFiltering" class="mx-2">
                <template #apply>Apply</template>
            </TodoButton>

        </div>
    </div>
</template>


