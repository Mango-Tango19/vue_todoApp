<template>
  <AddTask v-if="showAddTask" @add-task="addTask" />

  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import Tasks from "../components/Tasks";
import AddTask from "../components/AddTask";

export default {
  name: "Home",
  components: {
    Tasks,
    AddTask,
  },
  props: {
    showAddTask: Boolean,
  },
  data() {
    return {
      tasks: [],
    };
  },
  methods: {
    async deleteTask(id) {
      const res = await fetch(`api/tasks/${id}`, {
        method: "DELETE",
      });

      res.status === 200
        ? (this.tasks = this.tasks.filter((task) => task.id !== id))
        : alert("Error deleting task");
    },
    async toggleReminder(id) {
      const taskToToggle = await this.getTask(id);
      const updTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

      const res = await fetch(`api/tasks/${id}`, {
        method: "PUT",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(updTask),
      });

      const data = await res.json();

      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: data.reminder } : task
      );
    },
    async addTask(task) {
      const res = await fetch("api/tasks", {
        method: "POST",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(task),
      });

      const data = await res.json();

      this.tasks = [...this.tasks, data];
    },
    async getTasks() {
      const res = await fetch("api/tasks");

      const tasks = await res.json();

      return tasks;
    },
    async getTask(id) {
      const res = await fetch(`api/tasks/${id}`);

      const tasks = await res.json();

      return tasks;
    },
  },
  async created() {
    this.tasks = await this.getTasks();
  },
};
</script>