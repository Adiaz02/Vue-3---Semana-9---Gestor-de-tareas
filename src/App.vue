<template>
  <div class="container">
    <h1>Gestor de tareas</h1>

    <!-- Input con v-model y Enter -->
    <div class="input-row">
      <input
        v-model="newTask"
        @keyup.enter="addTask"
        placeholder="Escribe el nombre de la tarea"
      />
      <button @click="addTask">Agregar</button>
    </div>

    <hr />

    <!-- Mensaje si no hay tareas -->
    <div v-if="totalTasks === 0" class="empty">
      <p>No hay tareas registradas.</p>
    </div>

    <!-- Tablero -->
    <div v-else class="board">
      <!-- TO DO -->
      <section class="column todo">
        <h2>To do</h2>
        <ul>
          <li v-for="task in todo" :key="task.id" class="task">
            <span class="text">{{ task.title }}</span>
            <div class="actions">
              <!-- Solo mover a Doing -->
              <button @click="moveToDoing(task.id)" title="Pasar a Doing">➜</button>
            </div>
          </li>
        </ul>
        <p class="count">Total: {{ todoCount }}</p>
      </section>

      <!-- DOING (verde) -->
      <section class="column doing">
        <h2>Doing</h2>
        <ul>
          <li v-for="task in doing" :key="task.id" class="task">
            <span class="text">{{ task.title }}</span>
            <div class="actions">
              <!-- Solo mover a Done -->
              <button @click="moveToDone(task.id)" title="Pasar a Done">➜</button>
            </div>
          </li>
        </ul>
        <p class="count">Total: {{ doingCount }}</p>
      </section>

      <!-- DONE (rojo) -->
      <section class="column done">
        <h2>Done</h2>
        <ul>
          <li v-for="task in done" :key="task.id" class="task done">
            <span class="check">✅</span>
            <span class="text">{{ task.title }}</span>
          </li>
        </ul>
        <p class="count">Total: {{ doneCount }}</p>
      </section>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const newTask = ref('')
const todo = ref([])
const doing = ref([])
const done = ref([])
let nextId = 1

// contadores computados
const todoCount = computed(() => todo.value.length)
const doingCount = computed(() => doing.value.length)
const doneCount = computed(() => done.value.length)
const totalTasks = computed(() => todoCount.value + doingCount.value + doneCount.value)

const addTask = () => {
  const title = newTask.value.trim()
  if (!title) return
  todo.value.push({ id: nextId++, title })
  newTask.value = ''
}

const moveToDoing = (id) => {
  const idx = todo.value.findIndex(t => t.id === id)
  if (idx === -1) return
  const [task] = todo.value.splice(idx, 1)
  doing.value.push(task)
}

const moveToDone = (id) => {
  const idx = doing.value.findIndex(t => t.id === id)
  if (idx === -1) return
  const [task] = doing.value.splice(idx, 1)
  done.value.push(task)
}
</script>

<style scoped>
.container {
  max-width: 900px;
  margin: 24px auto;
  font-family: "Inter", Arial, sans-serif;
  padding: 0 16px;
}

h1 { text-align: center; margin-bottom: 12px; }

.input-row {
  display: flex;
  gap: 8px;
  justify-content: center;
  margin-bottom: 12px;
}

input {
  flex: 1;
  min-width: 220px;
  padding: 8px 10px;
  border-radius: 6px;
  border: 1px solid #ccc;
  font-size: 14px;
}

button {
  padding: 8px 12px;
  border-radius: 6px;
  border: none;
  cursor: pointer;
  background: #2b8aef;
  color: white;
  font-weight: 600;
}

hr { margin: 16px 0; }

.empty { text-align: center; color: #666; }

.board {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 14px;
}

.column {
  background: #fafafa;
  border-radius: 8px;
  padding: 12px;
  min-height: 120px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.04);
}

.column h2 { margin: 0 0 8px 0; }

ul { list-style: none; padding: 0; margin: 0; }

.task {
  display: flex;
  align-items: center;
  background: white;
  padding: 8px;
  margin-bottom: 8px;
  border-radius: 6px;
  border: 1px solid #eee;
}

/* Colores por columna */
.column.todo { background: #ffffff; border: 1px solid #eee; }
.column.doing { background: #e8f9e8; border: 1px solid #8bc34a; } /* verde */
.column.done { background: #fdeaea; border: 1px solid #f44336; }   /* rojo */

.task.done .text { text-decoration: line-through; opacity: 0.9; }

.check { margin-right: 8px; font-size: 16px; }

.actions { margin-left: auto; }
.actions button {
  background: #2b8aef;
  color: white;
  padding: 6px 8px;
  border-radius: 6px;
  font-weight: 600;
  cursor: pointer;
}

.count {
  margin-top: 8px;
  font-size: 13px;
  color: #444;
  text-align: right;
}
.text { word-break: break-word; flex: 1; }
</style>