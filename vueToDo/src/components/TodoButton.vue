<script >
export default {
    name: "TodoButton"
}
</script>
<template>
    <div>
        <!-- Buttons will have same class -->
        <button class="text-white text-sm  bg-pink-600 rounded-lg p-2">
            <!-- Named slots to access the same class -->
            <slot name="add"> </slot>
            <slot name="editTodo"> </slot>
            <slot name="deleteTodo"> </slot>
            <slot name="showAll"> </slot>
            <slot name="hide"> </slot>
            <slot name="cancel"> </slot>
            <slot name="addNewCategory"></slot>
            <slot name="filter"></slot>
            <slot name="apply"></slot>
        </button>


    </div>
</template>

