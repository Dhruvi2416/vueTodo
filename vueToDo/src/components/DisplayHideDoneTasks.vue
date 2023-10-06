

<script>
export default {
    name: "DisplayHideDoneTasks",
    props: {
        hide: Boolean
    },
    data() {
        return {

            hideCompleted: this.hide
        }
    },
    methods: {
        handleHideCompletedTask() {
            this.$emit("emitShow", this.hideCompleted)
            
            return this.hideCompleted
        }
    }

}
</script>
<template>
    <div>

        <div class="flex items-center">
            <input id="link-checkbox" type="checkbox" v-model="this.hideCompleted" @click="handleHideCompletedTask"
                class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 ">
            <label for="link-checkbox" class="ml-2 text-sm font-bold text-gray-900 dark:text-gray-300">Hide
                Completed</label>
        </div>

    </div>
</template>