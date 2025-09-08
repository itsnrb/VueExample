<template>
  <div class="task-card" :draggable="props.draggable" @dragstart="onDragStart($event)"
  @dragend="onDragEnd" tabindex="0">
    <div class="meta">
      <div style="display:flex;gap:8px;align-items:center">
        <div>{{ task.title }}</div>
        <div class="badge">{{ task.estimate }}</div>
      </div>
      <div class="small">{{ task.desc }}</div>
      <div style="margin-top:4px">
        <span v-for="tag in task.tags" :key="tag" class="badge">#{{tag}}</span>
      </div>
    </div>
    <div class="status">
      <div :class="['dot', statusClass]"></div>
      <div class="small">{{ task.status }}</div>
    </div>
  </div>
</template>

<script setup>
import { toRef } from 'vue'
const statusClass = ([]);
const props = defineProps({ task: Object, draggable: Boolean })
const emit = defineEmits(['dragstart', 'dragend'])
const task = toRef(props,'task')
const isDraggable = true

function onDragStart(e){
  console.log('Drag gestartet:', task.value.id)
  e.dataTransfer.effectAllowed = 'move'
  e.dataTransfer.setData('text/plain', task.value.id)
  emit('dragstart', task.value)
}

function onDragEnd(e){
  emit('dragend')
}
</script>