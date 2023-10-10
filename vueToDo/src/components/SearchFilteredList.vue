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
            //this following loop is done to make all the rest of the values false so that none can remove any filter on category
            if (e.target.value == "") {
                for (const category of Object.keys(this.categoryList)) {
                    this.searchCategory[category] = false;
                }
                //if selected is None then whatever comes in  i.t "" value turn it to false
                this.searchCategory[e.target.value] = false;
            }
            //if the given category is selected then deselect it and if deselected select it
            else {
                this.searchCategory[e.target.value] = !this.searchCategory[e.target.value]
            }
        }
    },

}
</script>

<template>
    <div class="flex ">
        <!-- text search  -->
        <input type="text" placeholder="Search Task" v-model="searchTask" class="font-semibold border-2 h-10 pl-2">

        <!-- Color Search -->
        <label for="underline_select" class="sr-only">Underline select</label>
        <select id="underline_select" v-model="searchColor"
            class="h-10 text-black p-2 font-semibold mx-4 block py-2.5 px-2 w-full text-sm text-black-500 bg-transparent  border-2 border-black-200   ">

            <option selected disabled class="text-black p-2 font-semibold" >Search Color</option>
            <option v-for="color in colorsList" :key="generateKey(color)" class="text-white"
                :style="{ backgroundColor: color }">{{ color }}</option>
            <option value="">None</option>
        </select>

        <!-- Category Search multiple -->
        <select @change="handleSelectCategory" multiple id="underline_select "
            class="h-24  mx-4 block py-2.5 px-2 w-96 text-sm text-black-500 bg-transparent  border-2 border-black-200   ">

            <option selected disabled class="text-black p-2 font-semibold">Search Category</option>
            <option :selected="searchCategory[category]" v-for="(category) in Object.keys(categoryList)"
                :key="generateKey(category)" class="py-1">{{ category }}</option>
            <option value="" class="font-semibold ">None</option>

        </select>
        <div class="flex ">
            <TodoButton @click="emitSearchFiltering" class="mx-2">
                <template #apply>Apply</template>
            </TodoButton>
        </div>
    </div>
</template>


