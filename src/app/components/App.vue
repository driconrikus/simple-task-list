<template>
<div>
<nav class="navbar navbar-dark bg-dark d-flex justify-content-center">
    <a href="/" class="navbar-brand">Simple Task List</a>
</nav>

<div class="container d-flex justify-content-center">
    <div class="row pt-5">
        <div class="col-md-5">
            <div class="card">
                <div class="card-body">
                    <form @submit.prevent="sendTask">
                        <div class="form-group">
                            <input type="text"
                            placeholder="Task title"
                            v-model="task.title"
                            class="form-control">
                        </div>
                        <div class="form-group">
                            <textarea id="" cols="30"
                            rows="10" class="form-control" 
                            placeholder="Describe your task"
                            v-model="task.description"></textarea>
                        </div>
                        <template v-if="edit === false">
                            <button class="btn btn-outline-primary btn-block">
                                Send
                            </button>
                        </template>
                        <template v-else>
                            <button class="btn btn-outline-primary btn-block">
                                Update
                                </button>
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
                    </tr>
                    </thead>
                    <tbody>
                        <tr v-for="task of tasks">
                            <td>{{task.title}}</td>
                            <td>{{task.description}}</td>
                            <td>
                                <button @click="deleteTask(task._id)" class="btn btn-outline-danger">
                                    Delete
                                </button>
                                <button @click="editTask(task._id)" class="btn btn-outline-secondary">
                                    Edit
                                </button>
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
    constructor(title, description) {
        this.title = title;
        this.description = description;
    }
}

export default {
    data() {
        return{
            task: new Task(),
            tasks: [],
            edit: false,
            taskToEdit: ''
        }
    },
    created() {
        this.getTasks();
    },
    methods: {
        sendTask() {
        if(this.edit === false) {
            fetch('/api/tasks', {
                method: 'POST',
                body: JSON.stringify(this.task),
                headers: {
                    'Accept': 'application/json',
                    'Content-type': 'application/json'
                }
            })
            .then(res => res.json())
            .then(data => {
                this.getTasks()
            });
        } 
        else {
            fetch('/api/tasks/' + this.taskToEdit, {
                method: 'PUT',
                body: JSON.stringify(this.task),
                headers: {
                    'Accept': 'application/json',
                    'Content-type': 'application/json'
                }
            })
            .then(res => res.json())
            .then(data => {
                this.getTasks()
                this.edit = false;
                })
        }
            this.task = new Task()
        },
        getTasks() {
            fetch('/api/tasks')
                .then(res => res.json())
                .then(data => {
                    this.tasks = data;
                    console.log(this.tasks)
                });
        },
        deleteTask(id) {
            fetch('/api/tasks/' + id, {
                method: 'DELETE',
                headers: {
                    'Accept': 'application/json',
                    'Content-type': 'application/json'
                }
            })
            .then(res => res.json())
            .then(data => {
                this.getTasks()
                });
        },
        editTask(id) {
            fetch('/api/tasks/' + id)
                .then(res => res.json())
                .then(data => {
                    this.task = new Task(data.title, data.description);
                    this.taskToEdit = data._id;
                    this.edit = true;
                });
        }
    }
}
</script>