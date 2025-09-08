<script setup>
import { ref } from "vue";
import Navbar from "./components/Navbar.vue";
import TaskCard from "./components/TaskCard.vue";
import MyTasks from "./components/MyTasks.vue";
import MyTasksView from "./components/MyTasksView.vue";
import Done from "./components/Done.vue";

const activeTab = ref("market");
const isDraggingOver = ref(false);
const tasks = ref([
  {
    id: "t1",
    title: "Design login",
    desc: "Login screen UI",
    status: "todo",
    estimate: "1d",
    tags: ["ui", "auth"],
  },
  {
    id: "t2",
    title: "Setup API",
    desc: "Backend API endpoints",
    status: "todo",
    estimate: "2d",
    tags: ["backend"],
  },
  {
    id: "t3",
    title: "Fix bug #42",
    desc: "Resolve crashing issue",
    status: "todo",
    estimate: "3h",
    tags: ["bug"],
  },
]);
const myTasks = ref([]);
const doneTasks = ref([]);
const recent = ref([]);

let dragged = null;

function switchTabs(page) {
  activeTab.value = page;
  console.log("Aktuelle Seite: " + page);
}

function dragStart(e, task) {
  console.log("Dragging:", task.id);
  dragged = task;
  if (e.dataTransfer) {
    e.dataTransfer.setData("text/plain", task.id);
    e.dataTransfer.effectAllowed = "move";
  }
}
function dragStartDone(e, t) {
  console.log("Dragging:", t.id);
  dragged = t;
  if (e.dataTransfer) {
    e.dataTransfer.setData("text/plain", t.id);
    e.dataTransfer.effectAllowed = "move";
  }
}

function dragEnd() {
  dragged = null;
  isDraggingOver.value = false;
}

function onDrop(e) {
  isDraggingOver.value = false;
  const id = e.dataTransfer?.getData("text/plain");
  const taskToAdd = tasks.value.find((t) => t.id === id) || dragged;
  if (taskToAdd && !myTasks.value.find((t) => t.id === taskToAdd.id)) {
    myTasks.value.push({ ...taskToAdd });
    recent.value.unshift(`Added "${taskToAdd.title}"`);
  }
}

function onDropDone(e) {
  console.log("Dropped!");
  isDraggingOver.value = false;
  const id = e.dataTransfer?.getData("text/plain");
  console.log("Data" + id);
  const taskToAdd = myTasks.value.find((t) => t.id === id) || dragged;
  if (taskToAdd && !doneTasks.value.find((t) => t.id === taskToAdd.id)) {
    doneTasks.value.push({ ...taskToAdd });
    recent.value.unshift(`Added to Done Tasks "${taskToAdd.title}"`);
    console.log('DoneTasks', doneTasks)
  }
}

function removeFromMyTasks(id) {
  const idx = myTasks.value.findIndex((t) => t.id === id);
  if (idx > -1) myTasks.value.splice(idx, 1);
}

function removeFromDone(id) {
  const idx = doneTasks.value.findIndex((t) => t.id === id);
  if (idx > -1) doneTasks.value.splice(idx, 1);
}

function changeStatus({ id, status }) {
  const t = myTasks.value.find((x) => x.id === id);
  if (t) t.status = status;
}

function resetAll() {
  myTasks.value = [];
  recent.value.unshift("Reset all tasks");
}


</script>

<template>
  <div class="app-shell">
    <Navbar :active-tab="activeTab" @change-tab="activeTab = $event" @reset="resetAll" />

    <div class="layout">
      <!-- Left column: Marketplace -->
      <div>
        <div class="panel">
          <div v-if="activeTab === 'market'">
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
                @dragstart="dragStart($event, task)"
                @dragend="dragEnd"
              />
            </div>
          </div>
          <div v-else>
            <div class="task-pool">
              <MyTasksView :tasks="myTasks" @remove="removeFromMyTasks" />
            </div>
          </div>
        </div>

        <div class="panel" style="margin-top: 12px">
          <h3>Activity</h3>
          <ul>
            <li v-for="(a, i) in recent" :key="i">{{ a }}</li>
            <li v-if="recent.length === 0" class="empty">No recent activity yet</li>
          </ul>
        </div>
      </div>

      <!-- Right column: My DoneTasks -->
      <div v-if="activeTab === 'my'">
        <aside class="right-col">
          <div class="panel">
            <h3>Done Tasks</h3>
            <div
              class="dropzone"
              @dragover.prevent
              @dragenter.prevent="isDraggingOver = true"
              @dragleave.prevent="isDraggingOver = false"
              @drop.prevent="onDropDone"
              :class="{ dragging: isDraggingOver }"
            >
              <Done
                :done="doneTasks"
                @undo="removeFromDone"
              />
              <div v-if="doneTasks.length === 0" class="empty">Drop tasks here.</div>
            </div>
          </div>
        </aside>
      </div>

      <!-- Right column: My Tasks -->
      <div v-if="activeTab === 'market'">
        <aside class="right-col">
          <div class="panel">
            <h3>My Tasks</h3>
            <div
              class="dropzone"
              @dragover.prevent
              @dragenter.prevent="isDraggingOver = true"
              @dragleave.prevent="isDraggingOver = false"
              @drop.prevent="onDrop"
              :class="{ dragging: isDraggingOver }"
            >
              <MyTasks
                :tasks="myTasks"
                @change-status="changeStatus"
                @remove="removeFromMyTasks"
              />
              <div v-if="myTasks.length === 0" class="empty">Drop tasks here.</div>
            </div>
          </div>
        </aside>
      </div>
    </div>
  </div>
</template>
