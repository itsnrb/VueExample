<template>
  <nav class="navbar">
    <div class="brand">
      <div class="logo">TO</div>
      <div>
        <div>Task Organizer</div>
        <div class="small">Vue 3 demo</div>
      </div>
    </div>

    <div class="nav-links">
      <button :class="{active: activeTab==='market'}" @click="$emit('change-tab','market')">Marketplace</button>
      <button :class="{active: activeTab==='my'}" @click="$emit('change-tab','my')">My Tasks</button>
    </div>

    <div class="controls">
      <button class="small-btn" @click="$emit('reset')">Reset</button>
    </div>
  </nav>
</template>

<script setup>
defineProps({ activeTab: String })
</script>