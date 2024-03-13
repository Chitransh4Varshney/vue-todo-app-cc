<template>
  <div class="container">
    <!-- Heading -->
    <h2 class="text-center mt-5">Vue To-do List App</h2>

    <!-- Display Date of Creation -->
    <p class="text-center mt-3">Date of Creation: {{ creationDate }}</p>

    <!-- Input -->
    <div class="input-group mt-5">
      <input
        type="text"
        v-model="task"
        placeholder="Enter task"
        class="form-control"
      />
      <div class="input-group-append">
        <button class="btn btn-warning" @click="addTask">
          ADD
        </button>
      </div>
    </div>

    <!-- Task table -->
    <table class="table table-bordered table-striped mt-4">
      <thead class="thead-dark">
        
        <tr>
          <th scope="col" class="text-center">Task</th>
          <th scope="col" class="text-center">Status</th>
          <th scope="col" class="text-center">Delete</th>
          <th scope="col" class="text-center">Update</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index">
          <td>
            <span :class="{ 'line-through': task.status === 'finished' }">
              {{ task.name }}
            </span>
          </td>
          <td>
            <span
              class="badge"
              @click="changeStatus(index)"
              :class="{
                'badge-danger': task.status === 'to-do',
                'badge-success': task.status === 'finished',
                'badge-warning': task.status === 'in-progress',
              }"
            >
              {{ capitalizeFirstChar(task.status) }}
            </span>
          </td>
          <td class="text-center">
            <button class="btn btn-danger" @click="deleteTask(index)">
              <span class="fa fa-trash"></span>
            </button>
          </td>
          <td class="text-center">
            <button class="btn btn-primary" @click="editTask(index)">
              <span class="fa fa-pencil"></span>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },

  data() {
    return {
      task: "",
      editedTask: null,
      creationDate: new Date().toLocaleDateString(),

      statuses: ["to-do", "in-progress", "finished"],
      tasks: [],
    };
  },

  created() {
    // Load tasks from local storage when component is created
    this.loadTasks();
  },


  methods: {
    capitalizeFirstChar(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },

    changeStatus(index) {
      let newIndex = this.statuses.indexOf(this.tasks[index].status);
      if (++newIndex > 2) newIndex = 0;
      this.tasks[index].status = this.statuses[newIndex];
      this.saveTasks(); // Save tasks after updating
    },

    deleteTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks(); // Save tasks after deletion
    },

    editTask(index) {
      this.task = this.tasks[index].name;
      this.editedTask = index;
    },

    addTask() {
      if (this.task.length === 0) return;

      if (this.editedTask != null) {
        this.tasks[this.editedTask].name = this.task;
        this.editedTask = null;
      } else {
        this.tasks.push({
          name: this.task,
          status: "to-do",
          created_at: new Date().toLocaleString(),
        });
      }

      this.task = "";
      this.saveTasks(); // Save tasks after submission
    },

    loadTasks() {
      const savedTasks = localStorage.getItem('tasks');
      if (savedTasks) {
        this.tasks = JSON.parse(savedTasks);
      }
    },


    saveTasks() {
      // Save tasks to local storage
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
  },
};
</script>

<style scoped>
.line-through {
  text-decoration: line-through;
}
*{
    margin: 0;
    padding: 0;
    text-align:left;
    background-color: lightslategray;
}
.text-center{
    background-color: #081b31;
    color: #fff;
    height: 5rem;
    line-height: 4rem;
}
</style>
