<template>
  <div class="app-shell">
    <Navbar :active-tab="activeTab" @change-tab="activeTab = $event" @reset="resetAll" />

    <div class="layout">
      <!-- Left column: Marketplace -->
      <div>
        <div class="panel">
          <header class="panel-header">
            <h2>Task Marketplace</h2>
            <div class="small">Drag tasks into "My Tasks"</div>
          </header>

          <div class="task-pool">
            <TaskCard
              v-for="task in tasks"
              :key="task.id"
              :task="task"
              draggable
              @dragstart.prevent="dragStart($event, task)"
              @dragend="dragEnd"
            />
          </div>
        </div>

        <div class="panel" style="margin-top:12px">
          <h3>Activity</h3>
          <ul>
            <li v-for="(a,i) in recent" :key="i">{{ a }}</li>
            <li v-if="recent.length===0" class="empty">No recent activity yet</li>
          </ul>
        </div>
      </div>

      <!-- Right column: My Tasks -->
      <aside class="right-col">
        <div class="panel">
          <h3>My Tasks</h3>
          <div class="dropzone"
               @dragover.prevent
               @dragenter.prevent="isDraggingOver=true"
               @dragleave.prevent="isDraggingOver=false"
               @drop.prevent="onDrop"
               :class="{ dragging: isDraggingOver }"
          >
            <MyTasks
              :tasks="myTasks"
              @change-status="changeStatus"
              @remove="removeFromMyTasks"
            />
            <div v-if="myTasks.length===0" class="empty">Drop tasks here.</div>
          </div>
        </div>
      </aside>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Navbar from './components/Navbar.vue'
import TaskCard from './components/TaskCard.vue'
import MyTasks from './components/MyTasks.vue'

const activeTab = ref('market')
const isDraggingOver = ref(false)
const tasks = ref([
  { id:'t1', title:'Design login', desc:'Login screen UI', status:'todo', estimate:'1d', tags:['ui','auth'] },
  { id:'t2', title:'Setup API', desc:'Backend API endpoints', status:'todo', estimate:'2d', tags:['backend'] },
  { id:'t3', title:'Fix bug #42', desc:'Resolve crashing issue', status:'todo', estimate:'3h', tags:['bug'] },
])
const myTasks = ref([])
const recent = ref([])

let dragged = null

function dragStart(e, task){
  dragged = task
  if(e.dataTransfer){
    e.dataTransfer.setData('text/plain', task.id)
    e.dataTransfer.effectAllowed = 'move'
  }
}

function dragEnd(){
  dragged = null
  isDraggingOver.value = false
}

function onDrop(e){
  isDraggingOver.value = false
  const id = e.dataTransfer?.getData('text/plain')
  const taskToAdd = tasks.value.find(t => t.id===id) || dragged
  if(taskToAdd && !myTasks.value.find(t => t.id===taskToAdd.id)){
    myTasks.value.push({...taskToAdd})
    recent.value.unshift(`Added "${taskToAdd.title}"`)
  }
}

function removeFromMyTasks(id){
  const idx = myTasks.value.findIndex(t => t.id===id)
  if(idx > -1) myTasks.value.splice(idx,1)
}

function changeStatus({id,status}){
  const t = myTasks.value.find(x => x.id===id)
  if(t) t.status = status
}

function resetAll(){
  myTasks.value = []
  recent.value.unshift('Reset all tasks')
}
</script>