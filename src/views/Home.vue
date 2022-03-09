<template>
    <div>
        <AddTask 
            v-show="showAddTask"
            @add-task="addTask" 
        />
        <Tasks 
            @toggle-reminder="toggleReminder" 
            @delete-task="deleteTask" 
            :tasks="tasks" 
        />
    </div>
</template>

<script>
import Tasks from "../components/Tasks";
import AddTask from "../components/AddTask";
export default {
    name: "Home",
    props: {
        showAddTask: Boolean
    },
    components: {
        Tasks,
        AddTask
    },
    data() {
        return {
            tasks: []
        }
    },
    methods: {
        async addTask(task){
        const res = await fetch(`api/tasks`, {
            method: "POST",
            headers: {
            "Content-Type": "application/json",
            },
            body: JSON.stringify(task)
        })
        const data = await res.json();

        this.tasks = [...this.tasks, data];
        },

        async deleteTask(id) {
        // console.log("task", id);
        if(confirm("Are you sure?")){
            const res = await fetch(`api/tasks/${id}`, {
            method: "DELETE"
            })

            res.status === 200 ? (this.tasks = this.tasks.filter(task => task.id !== id)) : alert("Error deleting tasks");
        }
        },

        async toggleReminder(id){
        // console.log(id);
        const taskToToggle = await this.fetchTask(id);
        const updTask = {...taskToToggle, reminder: !taskToToggle.reminder};
        const res = await fetch(`api/tasks/${id}`, {
            method: "PUT",
            headers: {
            "Content-Type": "application/json"
            },
            body: JSON.stringify(updTask)
        });
        const data = await res.json();

        this.tasks = this.tasks.map(task => task.id === id ? {...task, reminder: data.reminder} : task);
        },

        async fetchTasks() {
        const res = await fetch(`http://localhost:5000/tasks`);
        const data = await res.json();
        return data;
        },

        async fetchTask(id) {
        const res = await fetch(`api/tasks/${id}`);
        const data = await res.json();
        return data;
        },
  },
  async created() {
    this.tasks = await this.fetchTasks();

    // hard-coded data
    // this.tasks = [
    //   {
    //     id: 1,
    //     text: "Doctors Appointment",
    //     day: "March 1st at 2:30pm",
    //     reminder: true
    //   },
    //   {
    //     id: 2,
    //     text: "Meeting at School",
    //     day: "March 3rd at 1:30pm",
    //     reminder: true
    //   },
    //   {
    //     id: 3,
    //     text: "Food Shopping",
    //     day: "March 3rd at 11:00am",
    //     reminder: false
    //   },
    // ]
  }
}
</script>