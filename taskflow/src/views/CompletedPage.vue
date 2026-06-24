<script setup>
import { computed } from 'vue'
import { storeToRefs } from 'pinia'
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonList, IonItem, IonButton, IonIcon, IonCheckbox, IonLabel, IonThumbnail, IonImg } from '@ionic/vue'
import { trash } from 'ionicons/icons'
import { useTaskStore } from '@/stores/taskStore_2.js'

const taskStore = useTaskStore()
const { tasks } = storeToRefs(taskStore)
const { removeTask, toggleTask } = taskStore

const completedTasks = computed(() => tasks.value.filter(t => t.done === true))
</script>

<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Completed</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Completed</ion-title>
        </ion-toolbar>
      </ion-header>

      <div v-if="completedTasks.length === 0" class="no-tasks">
        <p>No completed tasks</p>
      </div>
      <ion-list v-else>
        <ion-item v-for="task in completedTasks" :key="task.id">
          <ion-thumbnail slot="start" v-if="task.photo">
            <ion-img :src="task.photo"></ion-img>
          </ion-thumbnail>
          <ion-checkbox slot="start" :checked="task.done" @ionChange="toggleTask(task.id)"></ion-checkbox>
          <ion-label :class="{ done: task.done }">{{ task.name }}</ion-label>
          <ion-button slot="end" fill="clear" color="danger" @click="removeTask(task.id)">
            <ion-icon slot="icon-only" :icon="trash"></ion-icon>
          </ion-button>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<style scoped>
.no-tasks { text-align: center; padding: 24px; color: #999; }
.done { text-decoration: line-through; color: #9ca3af; }
</style>
