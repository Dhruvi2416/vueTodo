<script >
export default {

    name: "TodoButton"
}
</script>
<template>
    <div>
        <!-- Buttons will have same class -->
        <button class="text-white text-sm  bg-green-600 rounded-full p-2">
            <!-- Named slots to access the same class -->
            <slot name="add"> </slot>
            <slot name="editTodo"> </slot>
            <slot name="deleteTodo"> </slot>
            <slot name="showAll"> </slot>
            <slot name="hide"> </slot>
            <slot name="cancel"> </slot>
        </button>


    </div>
</template>

