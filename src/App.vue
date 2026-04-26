<template>
  <div class="container">
    <h1>🚀 Vue Task Manager (Advanced)</h1>

    <!-- Add Task -->
    <div class="add-box">
      <input v-model="newTask" placeholder="Enter task..." />
      <button @click="addTask">Add</button>
    </div>

    <!-- Search -->
    <input v-model="search" placeholder="Search tasks..." class="search"/>

    <!-- Filters -->
    <div class="filters">
      <button @click="filter='all'">All</button>
      <button @click="filter='active'">Active</button>
      <button @click="filter='done'">Done</button>
    </div>

    <!-- Task List -->
    <ul>
      <li v-for="task in filteredTasks" :key="task.id">
        <span 
          :class="{done: task.done}" 
          @click="toggleTask(task.id)"
        >
          {{ task.text }}
        </span>

        <div>
          <button @click="editTask(task)">✏️</button>
          <button @click="deleteTask(task.id)">❌</button>
        </div>
      </li>
    </ul>

    <!-- Stats -->
    <div class="stats">
      <p>Total: {{ tasks.length }}</p>
      <p>Completed: {{ completedCount }}</p>
    </div>

    <!-- Edit Modal -->
    <div v-if="editing" class="modal">
      <div class="modal-box">
        <h3>Edit Task</h3>
        <input v-model="editText" />
        <button @click="updateTask">Save</button>
        <button @click="editing=false">Cancel</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",

  data() {
    return {
      newTask: "",
      search: "",
      filter: "all",
      tasks: [],
      editing: false,
      editId: null,
      editText: ""
    };
  },

  mounted() {
    const saved = localStorage.getItem("tasks");
    if (saved) {
      this.tasks = JSON.parse(saved);
    }
  },

  watch: {
    tasks: {
      handler(newTasks) {
        localStorage.setItem("tasks", JSON.stringify(newTasks));
      },
      deep: true
    }
  },

  computed: {
    filteredTasks() {
      let list = this.tasks;

      if (this.filter === "active") {
        list = list.filter(t => !t.done);
      } else if (this.filter === "done") {
        list = list.filter(t => t.done);
      }

      if (this.search) {
        list = list.filter(t =>
          t.text.toLowerCase().includes(this.search.toLowerCase())
        );
      }

      return list;
    },

    completedCount() {
      return this.tasks.filter(t => t.done).length;
    }
  },

  methods: {
    addTask() {
      if (!this.newTask.trim()) return;

      this.tasks.push({
        id: Date.now(),
        text: this.newTask,
        done: false
      });

      this.newTask = "";
    },

    deleteTask(id) {
      this.tasks = this.tasks.filter(t => t.id !== id);
    },

    toggleTask(id) {
      const task = this.tasks.find(t => t.id === id);
      task.done = !task.done;
    },

    editTask(task) {
      this.editing = true;
      this.editId = task.id;
      this.editText = task.text;
    },

    updateTask() {
      const task = this.tasks.find(t => t.id === this.editId);
      task.text = this.editText;
      this.editing = false;
    }
  }
};
</script>

<style>
body {
  background: #f5f5f5;
}

.container {
  max-width: 700px;
  margin: auto;
  font-family: Arial;
}

.add-box {
  margin-bottom: 10px;
}

input {
  padding: 8px;
  margin: 5px 0;
  width: 70%;
}

button {
  padding: 8px;
  margin-left: 5px;
  cursor: pointer;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  display: flex;
  justify-content: space-between;
  background: white;
  margin: 5px 0;
  padding: 10px;
  border-radius: 5px;
}

.done {
  text-decoration: line-through;
  color: gray;
}

.filters button {
  margin-right: 5px;
}

.stats {
  margin-top: 10px;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.5);
}

.modal-box {
  background: white;
  padding: 20px;
  margin: 100px auto;
  width: 300px;
}
</style>