<template lang="pug">
article(id="🔥TodoList")
    h1(id="🔥TodoList__Title") Todo List
    section(id="🔥TodoList__Add")
        input(id="🔥TodoList__Add__Input" type="text" v-model.trim="text" @keyup.enter="add_Todo")
        i(id="🔥TodoList__Add__Btn" class="fa-solid fa-plus fa-beat-fade fa-lg" @click="add_Todo")
    ul(id="🔥TodoList__TodoBox")
        li(id="🔥TodoList__TodoBox__Todo" v-for="todo in todoBox" :key="todo.id") 
            p(id="🔥TodoList__TodoBox__Todo__View")
                span(id="🔥TodoList__TodoBox__Todo__View__Text" @click="go_Edit(todo)") {{ todo.title }}
                i(id="🔥TodoList__TodoBox__Todo__View__DeleteBtn" class="fa-regular fa-trash-can fa-sm" @click.stop="delete_Todo(todo.id)")
            input(id="🔥TodoList__TodoBox__Todo__Edit" :class="{edit: todo.id === currentTodo.id}" type="text" v-model.trim="currentTodo.title" @focusout="edit_CancelTodo" @keyup.esc="edit_CancelTodo" @keyup.enter="edit_Todo(todo.id)")
</template>

<script setup>
import { ref } from 'vue';
import { v4 as uuidv4 } from 'uuid'
const text = ref('')
const currentTodo = ref({ id: '', title: '' })
const todoBox = ref([
    {
        id: uuidv4(),
        title: 'eat',
    },
    {
        id: uuidv4(),
        title: 'sleep',
    }
])

function add_Todo() {
    if (text.value === "") return

    todoBox.value.push({
        id: uuidv4(),
        title: text.value,
    })

    fetch('https://jsonplaceholder.typicode.com/posts/1', {
        method: 'PUT',
        body: JSON.stringify(todoBox.value),
        headers: {
            'Content-type': 'application/json; charset=UTF-8',
        },
    })
        .then((response) => response.json())
        .then((json) => console.log(json));

    text.value = ""
}
/**
 * @param {string} id uuidv4
 */
function edit_Todo(id) {
    todoBox.value.forEach(todo => todo.id === id ? todo.title = currentTodo.value.title : undefined)

    fetch('https://jsonplaceholder.typicode.com/posts/1', {
        method: 'PUT',
        body: JSON.stringify(todoBox.value),
        headers: {
            'Content-type': 'application/json; charset=UTF-8',
        },
    })
        .then((response) => response.json())
        .then((json) => console.log(json));

    // @ts-ignore
    currentTodo.value = {}
}
function edit_CancelTodo() {
    // @ts-ignore
    currentTodo.value = {}
}
/**
 * @param {string} id uuidv4
 */
function delete_Todo(id) {
    todoBox.value = todoBox.value.filter(todo => todo.id !== id)

    fetch('https://jsonplaceholder.typicode.com/posts/1', {
        method: 'PUT',
        body: JSON.stringify(todoBox.value),
        headers: {
            'Content-type': 'application/json; charset=UTF-8',
        },
    })
        .then((response) => response.json())
        .then((json) => console.log(json));

}
/** 
 * @param {object} todo 
 */
function go_Edit(todo) {
    // @ts-ignore
    currentTodo.value = { ...todo }
}


</script>

<style lang="sass">
#🔥TodoList
    display: grid
    place-content: center
    height: 100vh
    caret-color: transparent
    &__Title
        text-align: center
    &__Add
        display: flex
        justify-content: center
        align-items: center
        gap: 1.5vw
        &__Input
            border: 0
            border-radius: 5px
            outline: 2px solid gray
            caret-color: black
    &__TodoBox
        list-style-type: none
        padding-left: 0
        &__Todo
            position: relative
            font-size: 1.25rem
            &__View
                display: flex
                justify-content: space-between
                align-items: center
                border-bottom: 1px solid lightgray
                &__Text
                    width: 100%
            &__Edit
                position: absolute
                inset: 0
                width: 100%
                visibility: hidden
                caret-color: black
.edit
    visibility: visible !important
</style>
