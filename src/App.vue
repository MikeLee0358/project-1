<template lang="pug">
article(id="🔥TodoList")
    h1(id="🔥TodoList__Title") Todo List
    section(id="🔥TodoList__Add")
        input(id="🔥TodoList__Add__Input" type="text" v-model.trim="text" @keyup.enter="add_Todo")
        i(id="🔥TodoList__Add__Btn" class="fa-solid fa-plus fa-beat-fade fa-lg" @click="add_Todo")
    ul(id="🔥TodoList__TodoBox")
        li(id="🔥TodoList__TodoBox__Todo" v-for="(todo, index) in todoBox" :key="todo.id") 
            label(:id="'🔥TodoList__TodoBox__Todo__View' + index" :for="'🔥TodoList__TodoBox__Todo__Edit' + index")
                span(id="🔥TodoList__TodoBox__Todo__View__Text" @click="go_Edit(todo)") {{ todo.title }}
                i(id="🔥TodoList__TodoBox__Todo__View__DeleteBtn" class="fa-regular fa-trash-can fa-lg" @click.stop="delete_Todo(todo.id)")
            input(:id="'🔥TodoList__TodoBox__Todo__Edit' + index" :class="{edit: todo.id === currentTodo.id}" type="text" v-model.trim="currentTodo.title" @focusout="edit_CancelTodo" @keyup.esc="edit_CancelTodo" @keyup.enter="edit_Todo(todo.id)")
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
/** 透過id判斷要編輯的todo
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
    font-size: 1.5rem
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
            padding: 0.75vw
            outline: 2px solid gray
            caret-color: black
    &__TodoBox
        list-style-type: none
        padding-left: 0
        &__Todo
            position: relative
            & > label // 🔥TodoList__TodoBox__Todo__View' + {index}
                display: flex
                justify-content: space-between
                align-items: center
                border-bottom: 1px solid lightgray
                margin-bottom: 1.5vh
                > span  
                    width: 100% // 增加label點擊文字觸發範圍
            & > input // '🔥TodoList__TodoBox__Todo__Edit' + {index}
                display: none
                position: absolute
                inset: 0
                width: 100%
                padding: 0
                caret-color: black
.edit
    display: block !important

</style>
