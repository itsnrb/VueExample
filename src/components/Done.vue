<script setup>
const props = defineProps({ done: Array, draggable: Boolean })
const emit = defineEmits(['write-comment', 'undo'])
function emitWrite(id) { emit('write-comment', { id }) }
function emitUndo(id) { emit('undo', id) }

function onDragStart(e){
  e.dataTransfer.effectAllowed = 'move'
  e.dataTransfer.setData('text/plain', task.value.id)
  emit('dragstart', task.value)
}

function onDragEnd(e){
  emit('dragend')
}


</script>

<template>
    <div>
        <div v-for="d in done" :key="d.id" class="task-card" :draggable="draggable" @dragstart="onDragStart"
  @dragend="onDragEnd" >
            <div class="meta">
                <div>{{ d.title }}</div>
                <div class="small">{{ d.desc }}</div>
            </div>
            <button class="small-btn" @click="emitWrite(id)">Write comment</button>
            <button class="small-btn" @click="emitUndo(id)">Undo</button>
        </div>
    </div>
</template>
