<template>
  <div>
    <nav class="navbar navbar-light bg-light">
      <a href="/" class="navbar-brand">MEVN stack</a>
    </nav>

    <div class="container">
      <div class="row pt-5">
        <div class="col-md-5">
          <div class="card">
            <div class="card-body">
              <form @submit.prevent="sendTask">
                <div class="form-group">
                  <input
                    v-model="task.tittle"
                    type="text"
                    placeholder="Insert a task"
                    class="form-control"
                  />
                </div>
                <div class="form-group">
                  <textarea
                    v-model="task.description"
                    cols="30"
                    rows="10"
                    class="form-control"
                    placeholder="Insert a description"
                  ></textarea>
                </div>
                <template v-if="edit === false">
                  <button class="btn btn-primary btn-block">Send</button>
                </template>
                <template v-else>
                  <button class="btn btn-warning btn-block">Update</button>
                </template>
              </form>
            </div>
          </div>
        </div>
        <div class="col-md-7">
          <table class="table table-bordered">
            <thead>
              <tr>
                <th>Task</th>
                <th>Description</th>
                <th>Acciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="task in tasks" :key="task._id">
                <td>{{task.tittle}}</td>
                <td>{{task.description}}</td>
                <td>
                  <button class="btn btn-danger" @click="deleteTask(task._id)">Delete</button>
                  <button class="btn btn-secondary" @click="editTask(task._id)">Edit</button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
class Task {
  constructor(tittle, description) {
    this.tittle = tittle;
    this.description = description;
  }
}

export default {
  data() {
    return {
      task: new Task(),
      tasks: [],
      edit: false,
      taskToEdit:''
    };
  },
  created() {
    this.getTasks();
  },
  methods: {
    sendTask() {
      if (this.edit=== false) {
        fetch("/api/tasks", {
        method: "POST",
        body: JSON.stringify(this.task),
        headers: {
          "Accept": "application/json",
          "Content-type": "application/json"
        }
      })
        .then(res => res.json())
        .then(data => {
          this.getTasks();
        });
      } else {
        fetch('/api/tasks/'+this.taskToEdit,{
          method:'PUT',
          body:JSON.stringify(this.task),
          headers: {
          "Accept": "application/json",
          "Content-type": "application/json"
        }
        })
        .then(res=>res.json())
        .then(data=>{
          this.getTasks();
          this.edit=false;
        });
      }
      this.task = new Task();
    },
    getTasks() {
      fetch("/api/tasks")
        .then(res => res.json())
        .then(data => {
          this.tasks = data;
          console.log(this.tasks);
        });
    },
    deleteTask(id) {
      fetch("/api/tasks/" + id, {
        method: "DELETE",
        headers: {
          "Accept": "application/json",
          "Content-type": "application/json"
        }
      })
        .then(res => res.json())
        .then(data => {
          this.getTasks();
        });
    },
    editTask(id) {
      fetch("/api/tasks/" + id)
        .then(res => res.json())
        .then(data => {
          this.task = new Task(data.tittle, data.description);
          this.taskToEdit=data._id;
          this.edit = true;
        });
    }
  }
};
</script>