<script setup lang="ts">
import { nextTick, ref } from 'vue'

const title = ref<string>('To-Do')
interface TaskType {
  id: number
  label: string
  purchased: boolean
  editing: boolean
  inpShow: boolean
  priority : boolean
}
const tasks = ref<TaskType>(JSON.parse(localStorage.getItem('items')) || [])
const newTask = ref<string>('')
const taskPriority = ref<boolean>(false)
const completed = ref<boolean>(false)
const editing = ref<boolean>(false)
const inpShow = ref<boolean>(false)
const listInput = ref([])
const searchInput = ref([])
const search = ref<boolean>(false)
const searchWord = ref<string>('')
const dupTasks = ref<TaskType>([])
const saveToLocalStorage = () => {
  localStorage.setItem('items', JSON.stringify(tasks.value))
}
const addItem = () => {
  if (newTask.value.trim() === '') {
    return
  }
  tasks.value.push({
    id: tasks.value.length + 1,
    label: newTask.value,
    purchased: completed.value,
    editing: editing.value,
    inpShow: inpShow.value,
    priority : taskPriority.value
  })
  newTask.value = ''
  taskPriority.value=""
  saveToLocalStorage()
}
const deleteItem = async (index) => {
    alert("Are you sure you want to delete this task!!")
  tasks.value.splice(index, 1)
  saveToLocalStorage()
}
const editItem = async (index) => {
  tasks.value[index].editing = !tasks.value[index].editing
  tasks.value[index].inpShow = !tasks.value[index].inpShow
  await nextTick()
  listInput.value[index].focus()
  saveToLocalStorage()
}
const itemPurchased = (index) => {
  tasks.value[index].purchased = !tasks.value[index].purchased
  saveToLocalStorage()
}
const searchEnable = async () => {
  search.value = !search.value
  await nextTick()
  searchInput.value.focus()
  dupTasks.value = tasks.value
  searchWord.value = ''
}
const searching = () => {
  tasks.value = dupTasks.value.filter((task) =>
    task.label.toLowerCase().includes(searchWord.value.toLowerCase()),
  )
}
const searchItem = () => {
  searching()
}
</script>

<template>
    <div class="to-do">
        
        <h1>{{ title }}</h1>
        <div class="task-list">
            <input class="inplist" @keyup.enter="addItem" v-model="newTask" type="text" placeholder="Add items" />
            <button @click="addItem">add item</button>
            <label>
                <input v-model="taskPriority" type="checkbox" />
                High Priority
            </label>
            </br>
            <button @click="searchEnable">Search</button>
            <input ref="searchInput" type="text" v-show="search" @input="searchItem" v-model="searchWord" />
        </div>
  
  

  
  <ul>
    <li
      v-for="(task, index) in tasks"
      :key="task.id"
      :class="{ strikeout: task.purchased ,priority : task.priority }"
      @dblclick="editItem(index)"
    >
      <h4>{{ task.label }}</h4>
      <input
        :ref="(el) => (listInput[index] = el)"
        type="text"
        v-model="task.label"
        v-show="task.editing"
        @keyup.enter="editItem(index)"
      />
      <input @change="itemPurchased(index)" :checked="task.purchased" type="checkbox" />
      <button @click="deleteItem(index)">Delete</button>
      <button @click="editItem(index)">Edit</button>
    </li>
  </ul>
    </div>
  
</template>

<style>
.strikeout {
  text-decoration: line-through;
  color: darkgrey;
}
.priority{
    font-weight: bolder;
    color: rgb(156, 2, 2);
}
.to-do{
    display: grid;
    justify-content: center;
    align-items: center;

}
h1{
    text-align: center;
    
}
.task-list{
    display: flex;
    gap: 20px;
}
</style>
