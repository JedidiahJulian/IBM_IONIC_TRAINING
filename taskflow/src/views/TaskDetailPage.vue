<script setup>
import { computed } from 'vue'
import { useRoute } from 'vue-router'
import { storeToRefs } from 'pinia'
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonBackButton, IonButtons, IonCard, IonCardContent, IonCardHeader, IonCardTitle, IonButton, IonIcon } from '@ionic/vue'
import { trash } from 'ionicons/icons'
import { useTaskStore } from '@/stores/taskStore_2.js'

const route = useRoute()
const taskStore = useTaskStore()
const { tasks } = storeToRefs(taskStore)
const { removeTask, toggleTask } = taskStore

const taskId = computed(() => parseInt(String(route.params.id)))
const task = computed(() => tasks.value.find(t => t.id === taskId.value))

function handleDelete() {
  removeTask(taskId.value)
  window.history.back()
}

function handleToggle() {
  toggleTask(taskId.value)
}
</script>

<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-back-button default-href="/tabs/tasks"></ion-back-button>
        </ion-buttons>
        <ion-title>Task Details</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-buttons slot="start">
            <ion-back-button default-href="/tabs/tasks"></ion-back-button>
          </ion-buttons>
          <ion-title size="large">Task Details</ion-title>
        </ion-toolbar>
      </ion-header>

      <div v-if="task" class="task-detail">
        <ion-card>
          <ion-card-header>
            <ion-card-title>{{ task.name }}</ion-card-title>
          </ion-card-header>
          <ion-card-content>
            <div class="detail-row">
              <strong>ID:</strong>
              <span>{{ task.id }}</span>
            </div>
            <div class="detail-row">
              <strong>Status:</strong>
              <span :class="{ done: task.done }">{{ task.done ? 'Completed' : 'Pending' }}</span>
            </div>
            <div class="detail-row">
              <strong>Name:</strong>
              <span>{{ task.name }}</span>
            </div>
          </ion-card-content>
        </ion-card>

        <div class="action-buttons">
          <ion-button @click="handleToggle" expand="block" :color="task.done ? 'warning' : 'success'">
            {{ task.done ? 'Mark as Pending' : 'Mark as Done' }}
          </ion-button>
          <ion-button @click="handleDelete" expand="block" color="danger">
            <ion-icon slot="start" :icon="trash"></ion-icon>
            Delete Task
          </ion-button>
        </div>
      </div>
      <div v-else class="no-task">
        <p>Task not found</p>
      </div>
    </ion-content>
  </ion-page>
</template>

<style scoped>
.task-detail { padding: 16px; }
.detail-row { display: flex; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #eee; }
.detail-row strong { color: #333; }
.detail-row span { color: #666; }
.done { color: #9ca3af; text-decoration: line-through; }
.action-buttons { display: flex; flex-direction: column; gap: 12px; margin-top: 24px; }
.no-task { text-align: center; padding: 24px; color: #999; }
</style>
