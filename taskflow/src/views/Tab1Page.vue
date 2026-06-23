<!--
=============================================================
  DAY 4 ASSIGNMENT — TaskListView.vue (refactored for Pinia)
  Remove all local state. Use the store for everything.
=============================================================
-->
<script setup>
import { ref } from 'vue'
import { storeToRefs } from 'pinia'
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonList, IonItem, IonButton, IonFab, IonFabButton, IonIcon, IonCheckbox, IonLabel } from '@ionic/vue'
import { add } from 'ionicons/icons'

// TODO 1: Import your store
// import { useTaskStore } from '@/stores/taskStore'
import {useTaskStore} from '@/stores/taskStore_2.js'

// TODO 2: Get the store instance
// const taskStore = useTaskStore()
const taskStore = useTaskStore()

// TODO 3: Destructure REACTIVE STATE using storeToRefs()
// const { tasks, doneCount, pendingCount, totalCount } = storeToRefs(taskStore)
const { tasks, doneCount, pendingCount, totalCount } = storeToRefs(taskStore)

// TODO 4: Destructure ACTIONS directly (no storeToRefs needed for functions)
// const { addTask, toggleTask, removeTask } = taskStore
const { addTask, toggleTask, removeTask } = taskStore

// This local ref is fine — it's UI state, not task state
const newTaskName = ref('')

function handleAdd() {
  // TODO 5: Call addTask() from the store, then clear the input
  addTask(newTaskName.value)
  newTaskName.value = ''
}
</script>

<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Tasks</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Tasks</ion-title>
        </ion-toolbar>
      </ion-header>

      <!-- Stats section -->
      <div class="stats">
        Total: {{ totalCount }} | Done: {{ doneCount }} | Pending: {{ pendingCount }}
      </div>

      <!-- Input section -->
      <div class="input-section">
        <input v-model="newTaskName" placeholder="New task..." @keyup.enter="handleAdd" class="task-input" />
        <ion-button @click="handleAdd" expand="block">Add Task</ion-button>
      </div>

      <!-- Task list -->
      <div v-if="tasks.length == 0">
        <p class="no-tasks">No tasks available</p>
      </div>
      <ion-list v-else>
        <ion-item v-for="task in tasks" :key="task.id">
          <ion-checkbox slot="start" v-model="task.done" @ionChange="toggleTask(task.id)"></ion-checkbox>
          <ion-label :class="{ done: task.done }">{{ task.name }}</ion-label>
          <ion-button slot="end" fill="clear" color="danger" @click="removeTask(task.id)">Remove</ion-button>
        </ion-item>
      </ion-list>

      <!-- FAB for adding new tasks -->
      <ion-fab slot="fixed" vertical="bottom" horizontal="end">
        <ion-fab-button @click="handleAdd">
          <ion-icon :icon="add"></ion-icon>
        </ion-fab-button>
      </ion-fab>
    </ion-content>
  </ion-page>
</template>

<style scoped>
.stats { font-size: 13px; color: #555; padding: 12px 16px; background: #e9f7f0; margin: 16px 0; border-radius: 6px; }
.input-section { padding: 16px; }
.task-input { width: 100%; padding: 8px 12px; border: 1px solid #ddd; border-radius: 6px; font-size: 14px; margin-bottom: 8px; }
.no-tasks { text-align: center; padding: 24px; color: #999; }
.done { text-decoration: line-through; color: #9ca3af; }
</style>
