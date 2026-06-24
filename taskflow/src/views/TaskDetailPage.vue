<script setup>
import { computed, ref } from 'vue'
import { useRoute } from 'vue-router'
import { storeToRefs } from 'pinia'
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonBackButton, IonButtons, IonCard, IonCardContent, IonCardHeader, IonCardTitle, IonButton, IonIcon, IonImg } from '@ionic/vue'
import { trash, image } from 'ionicons/icons'
import { useTaskStore } from '@/stores/taskStore_2.js'

const route = useRoute()
const taskStore = useTaskStore()
const { tasks } = storeToRefs(taskStore)
const { removeTask, toggleTask, addPhotoToTask } = taskStore
const fileInput = ref(null)

const taskId = computed(() => parseInt(String(route.params.id)))
const task = computed(() => tasks.value.find(t => t.id === taskId.value))

function handleDelete() {
  removeTask(taskId.value)
  window.history.back()
}

function handleToggle() {
  toggleTask(taskId.value)
}

function compressImage(dataUrl, maxWidth = 400, maxHeight = 400, quality = 0.7) {
  return new Promise((resolve) => {
    const img = new Image()
    img.onload = () => {
      const canvas = document.createElement('canvas')
      let width = img.width
      let height = img.height

      if (width > height) {
        if (width > maxWidth) {
          height = Math.round((height * maxWidth) / width)
          width = maxWidth
        }
      } else {
        if (height > maxHeight) {
          width = Math.round((width * maxHeight) / height)
          height = maxHeight
        }
      }

      canvas.width = width
      canvas.height = height
      const ctx = canvas.getContext('2d')
      ctx?.drawImage(img, 0, 0, width, height)
      resolve(canvas.toDataURL('image/jpeg', quality))
    }
    img.src = dataUrl
  })
}

function triggerGallery() {
  console.log('Opening gallery')
  if (fileInput.value) {
    fileInput.value.click()
  } else {
    const input = document.querySelector('input[type="file"][id="galleryInput"]')
    input?.click()
  }
}

async function handleFileSelect(event) {
  console.log('File selected:', event)
  const file = event.target?.files?.[0]
  console.log('File object:', file)
  if (file) {
    const reader = new FileReader()
    reader.onload = async (e) => {
      const dataUrl = e.target?.result
      console.log('DataURL length:', dataUrl?.toString().length)
      if (typeof dataUrl === 'string') {
        console.log('Compressing image...')
        const compressed = await compressImage(dataUrl)
        console.log('Compressed image length:', compressed.length)
        addPhotoToTask(taskId.value, compressed)
        console.log('Photo added to task')
      }
    }
    reader.readAsDataURL(file)
  }
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

        <ion-card v-if="task.photo" class="photo-card">
          <ion-card-header>
            <ion-card-title>Photo</ion-card-title>
          </ion-card-header>
          <ion-card-content>
            <ion-img :src="task.photo" alt="Task photo" class="task-photo"></ion-img>
          </ion-card-content>
        </ion-card>

        <input
          id="galleryInput"
          ref="fileInput"
          type="file"
          accept="image/*"
          style="display: none"
          @change="handleFileSelect"
        />
        <div class="action-buttons">
          <ion-button @click="triggerGallery" expand="block" color="primary">
            <ion-icon slot="start" :icon="image"></ion-icon>
            {{ task.photo ? 'Change Photo' : 'Add Photo' }}
          </ion-button>
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
.photo-card { margin: 16px 0; }
.task-photo { width: 100%; max-height: 400px; object-fit: cover; border-radius: 6px; }
.action-buttons { display: flex; flex-direction: column; gap: 12px; margin-top: 24px; }
.no-task { text-align: center; padding: 24px; color: #999; }
</style>
