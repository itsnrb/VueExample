<script setup>
import { ref } from "vue";

const props = defineProps({ tasks: Array, draggable: Boolean });
const emit = defineEmits(["dragstart", "dragend", "remove"]);

const activeCommentId = ref(null); // speichert die ID der Task mit aktivem Kommentar
const description = ref("");
const isDraggingOver = ref(false);
let dragged = null;

function emitRemove(id) {
  emit("remove", id);
}

function writeComment(id) {
  console.log("Writing comment for " + id);
  activeCommentId.value = id; // nur diese Task öffnen
  const found = props.tasks.find((t) => t.id === id);
  if (found) {
    description.value = found.desc || found.description || "";
  } else {
    description.value = "";
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

function saveComment(id) {
  const found = props.tasks.find((t) => t.id === id);
  if (found) {
    found.desc = description.value;
  }
  activeCommentId.value = null; // nach Speichern wieder schließen
}
</script>

<template>
  <div>
    <h2>My Tasks</h2>
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

        <!-- Kommentarbereich -->
        <div v-if="activeCommentId !== t.id" style="margin-top: 15px" class="small">
          {{ t.desc }}
        </div>
        <div v-else>
          <input
            class="commentInput"
            v-model="description"
            @keyup.enter="saveComment(t.id)"
          />
        </div>

        <!-- Tags -->
        <div style="margin-top: 16px">
          <span v-for="tag in t.tags" :key="tag" class="badge">#{{ tag }}</span>
        </div>
      </div>

      <!-- Buttons -->
      <div>
        <button
          v-if="activeCommentId === t.id"
          class="small-btn"
          @click="saveComment(t.id)"
        >
          Save
        </button>
        <button
          v-else
          class="small-btn"
          @click="writeComment(t.id)"
        >
          Comment
        </button>
        <button class="small-btn" @click="emitRemove(t.id)">Remove</button>
      </div>
    </div>
  </div>
</template>