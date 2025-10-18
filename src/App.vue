<script setup>
import { ref, onMounted } from 'vue'
const nuevaTarea = ref('')
const nuevoFolder = ref('')
const selectedFolder = ref('')
const folders = ref([
 { id: 1, name: 'General', tasks: [
   { id: 1, texto: 'Aprender Vue.js' },
   { id: 2, texto: 'Construir una app' },
   { id: 3, texto: 'Desplegar con Nginx' }
 ] }
])
function agregarFolder() {
 if (nuevoFolder.value.trim() === '') return
 folders.value.push({ id: Date.now(), name: nuevoFolder.value, tasks: [] })
 nuevoFolder.value = ''
 guardarFolders()
}
function agregarTarea() {
 if (nuevaTarea.value.trim() === '' || !selectedFolder.value) return
 const folder = folders.value.find(f => f.id == selectedFolder.value)
 if (folder) {
   folder.tasks.push({ id: Date.now(), texto: nuevaTarea.value })
   nuevaTarea.value = ''
   guardarFolders()
 }
}
function eliminarTarea(folderId, taskId) {
 const folder = folders.value.find(f => f.id == folderId)
 if (folder) {
   folder.tasks = folder.tasks.filter(t => t.id !== taskId)
   guardarFolders()
 }
}
function guardarFolders() {
 localStorage.setItem('folders', JSON.stringify(folders.value))
}
function cargarFolders() {
 const foldersGuardados = localStorage.getItem('folders')
 if (foldersGuardados) {
   folders.value = JSON.parse(foldersGuardados)
 }
}
onMounted(() => {
 cargarFolders()
})
</script>

<template>
 <div class="container">
 <h1>Mi Lista de Tareas</h1>

 <div class="input-group">
 <input v-model="nuevoFolder" placeholder="Nombre de la carpeta">
 <button @click="agregarFolder">Agregar Carpeta</button>
 </div>

 <div class="input-group">
 <select v-model="selectedFolder">
 <option value="">Seleccionar Carpeta</option>
 <option v-for="folder in folders" :value="folder.id" :key="folder.id">{{ folder.name }}</option>
 </select>
 <input v-model="nuevaTarea" @keyup.enter="agregarTarea" placeholder="Añadir nueva tarea">
 <button @click="agregarTarea">Agregar</button>
 </div>

 <div v-for="folder in folders" :key="folder.id" class="folder">
 <h2>{{ folder.name }}</h2>
 <ul>
 <li v-for="tarea in folder.tasks" :key="tarea.id">
 {{ tarea.texto }}
 <button @click="eliminarTarea(folder.id, tarea.id)">Eliminar</button>
 </li>
 </ul>
 </div>
 </div>
</template>

<style scoped>
body {
  font-family: Arial, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.container {
  max-width: 600px;
  width: 100%;
  padding: 20px;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  color: #333;
  margin-bottom: 20px;
}

.input-group {
  display: flex;
  margin-bottom: 20px;
}

input, select {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

select {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
  margin-right: 10px;
}

button {
  padding: 10px 15px;
  border: none;
  background-color: #4CAF50;
  color: white;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
}

button:hover {
  background-color: #45a049;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #eee;
  background-color: #fafafa;
  margin-bottom: 5px;
  border-radius: 4px;
}

li:last-child {
  border-bottom: none;
}

li button {
  background-color: #f44336;
  border-radius: 4px;
  padding: 5px 10px;
}

li button:hover {
  background-color: #d32f2f;
}

.folder {
  margin-bottom: 30px;
}

.folder h2 {
  color: #555;
  border-bottom: 2px solid #ddd;
  padding-bottom: 5px;
}
</style>
