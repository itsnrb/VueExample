<template>
  <div>
    <div v-for="t in tasks" :key="t.id" class="task-card">
      <div class="meta">
        <div>{{ t.title }}</div>
        <div class="small">{{ t.desc }}</div>
      </div>
      <div>
        <select v-model="t.status" @change="emitChange(t)">
          <option value="todo">To Do</option>
          <option value="progress">In Progress</option>
          <option value="done">Done</option>
        </select>
        <button class="small-btn" @click="emitRemove(t.id)">Remove</button>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({ tasks: Array })
const emit = defineEmits(['change-status','remove'])
function emitChange(t){ emit('change-status',{ id:t.id,status:t.status }) }
function emitRemove(id){ emit('remove',id) }
</script>