<script setup>
import { toRef } from 'vue'
import { ref } from 'vue'

const props = defineProps({ tasks: Array, draggable: Boolean })
const emit = defineEmits(['remove', 'dragstart', 'dragend'])
function emitRemove(id) { emit('remove', id) }
const task = toRef(props,'tasks')
const isDraggingOver = ref(false);


let dragged = null;


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


</script>

<template>
  <div>
    <div
      v-for="t in tasks"
      :key="t.id"
      class="task-card"
      :draggable="true"
      @dragstart="dragStartDone($event, t)"
      @dragend="dragEnd"
    >
      <div class="meta">
        <div>{{ t.title }}</div>
        <div class="small">{{ t.desc }}</div>
      </div>
      <div>
        <button class="small-btn" @click="emitRemove(t.id)">Remove</button>
      </div>
    </div>
  </div>
</template>