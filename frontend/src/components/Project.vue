<template>
    <div style="padding-top: 5em">
        <h2>Project Details</h2>
        <ProjectForm class="form" @closeModal="closeModal" @refresh="refresh" v-bind:project="{ ...populated_project }" v-bind:available_users="available_users" />
    </div>

    <div class="task-header">
        <h2>Tasks</h2>
        <h2>
            <button style="margin-top: 50%" class="task-btn" @click="openTaskModal()">Add
                Task &#43;</button>
        </h2>
    </div>

    <div class="task-container">
        <div class="task-block">
            <h3>Assigned</h3>
            <div v-for="task in populated_project.tasks">
                <div v-if="task.status == 'assigned'" class="task-card assigned">
                    <div class="content">
                        <div style="display: flex; justify-content: space-between; padding-top: 8px">
                            <p class="title">{{ task.name }}</p>
                            <div style="display: flex; justify-content: space-between;">
                                <p class="icons" id="trash-can" @click="deleteTask(task._id)">&#x1F5D1;</p>
                                <p class="icons" @click="openTaskModal(task)">&#9998;</p>
                            </div>
                        </div>
                        <p class="details">{{ task.details }}</p>
                        <p>Priority: <span class="priority">{{ task.priority }}</span></p>
                        <hr />
                        <div>
                            <p>Assigned: <span class="priority">{{ task.timeline.assigned?.split("T")[0] }}</span></p>
                            <p>Due: <span class="priority">{{ task.timeline.due?.split("T")[0] }}</span></p>
                            <p>Last Updated: <span class="priority">{{ task.timeline.last_updated?.split("T")[0]
                            }}</span></p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="task-block">
            <h3>In-Progress</h3>
            <div v-for="task in populated_project.tasks">
                <div v-if="task.status == 'in-progress'" class="task-card in-progress">
                    <div class="content">
                        <div style="display: flex; justify-content: space-between; padding-top: 8px">
                            <p class="title">{{ task.name }}</p>
                            <div style="display: flex; justify-content: space-between;">
                                <p class="icons" id="trash-can" @click="deleteTask(task._id)">&#x1F5D1;</p>
                                <p class="icons" @click="openTaskModal(task)">&#9998;</p>
                            </div>
                        </div>
                        <p class="details">{{ task.details }}</p>
                        <p>Priority: <span class="priority">{{ task.priority }}</span></p>
                        <hr />
                        <div>
                            <p>Assigned: <span class="priority">{{ task.timeline.assigned?.split("T")[0] }}</span></p>
                            <p>Due: <span class="priority">{{ task.timeline.due?.split("T")[0] }}</span></p>
                            <p>Last Updated: <span class="priority">{{ task.timeline.last_updated?.split("T")[0]
                            }}</span></p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="task-block">
            <h3>In-Review</h3>
            <div v-for="task in populated_project.tasks">
                <div v-if="task.status == 'in-review'" class="task-card in-review">
                    <div class="content">
                        <div style="display: flex; justify-content: space-between; padding-top: 8px">
                            <p class="title">{{ task.name }}</p>
                            <div style="display: flex; justify-content: space-between;">
                                <p class="icons" id="trash-can" @click="deleteTask(task._id)">&#x1F5D1;</p>
                                <p class="icons" @click="openTaskModal(task)">&#9998;</p>
                            </div>
                        </div>
                        <p class="details">{{ task.details }}</p>
                        <p>Priority: <span class="priority">{{ task.priority }}</span></p>
                        <hr />
                        <div>
                            <p>Assigned: <span class="priority">{{ task.timeline.assigned?.split("T")[0] }}</span></p>
                            <p>Due: <span class="priority">{{ task.timeline.due?.split("T")[0] }}</span></p>
                            <p>Last Updated: <span class="priority">{{ task.timeline.last_updated?.split("T")[0]
                            }}</span></p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="task-block">
            <h3>Completed</h3>
            <div v-for="task in populated_project.tasks">
                <div v-if="task.status == 'completed'" class="task-card completed">
                    <div class="content">
                        <div style="display: flex; justify-content: space-between; padding-top: 8px">
                            <p class="title">{{ task.name }}</p>
                            <div style="display: flex; justify-content: space-between;">
                                <p class="icons" id="trash-can" @click="deleteTask(task._id)">&#x1F5D1;</p>
                                <p class="icons" @click="openTaskModal(task)">&#9998;</p>
                            </div>
                        </div>
                        <p class="details">{{ task.details }}</p>
                        <p>Priority: <span class="priority">{{ task.priority }}</span></p>
                        <hr />
                        <div>
                            <p>Assigned: <span class="priority">{{ task.timeline.assigned?.split("T")[0] }}</span></p>
                            <p>Due: <span class="priority">{{ task.timeline.due?.split("T")[0] }}</span></p>
                            <p>Last Updated: <span class="priority">{{ task.timeline.last_updated?.split("T")[0]
                            }}</span></p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <Modal v-bind:task="task_data" v-show="showModal" v-bind:available_users="available_users" @closeModal="closeModal" @refresh="refresh" v-bind:pID="populated_project._id" />
</template>

<script>
import Modal from './Modal.vue';
import ProjectForm from './ProjectForm.vue';

export default {
    name: 'Project',
    components: {
        Modal,
        ProjectForm,
    },
    data() {
        return {
            populated_project: {},
            available_users: [],
            showModal: false,
            task_data: {}
        }
    },
    methods: {
        openTaskModal(task) {
            this.showModal = true;
            this.task_data = task ? { ...task } : { timeline: {} };
        },

        closeModal() {
            this.showModal = false;
        },

        async deleteTask(id) {
            const res = await fetch(`http://localhost:8080/tasks/${id}`, {
                method: 'DELETE',
                mode: 'cors'
            });

            console.log(await res.json());
            this.refresh();
        },

        async setData(project) {
            this.populated_project = project;
            this.populated_project.managerName = this.populated_project.manager.first + " " + this.populated_project.manager.last;

            const usersRes = await fetch(`http://localhost:8080/users/?project=${project.id}`);
            const users = await usersRes.json();

            this.available_users = users.map(user => {
                return { _id: user._id, name: `${user.first} ${user.last}` }
            });

            for (let i = 0; i < this.populated_project.tasks.length; i++) {
                this.populated_project.tasks[i].timeline.assigned = this.populated_project.tasks[i].timeline.assigned?.slice(0, 19);
                this.populated_project.tasks[i].timeline.due = this.populated_project.tasks[i].timeline.due?.slice(0, 19);
                this.populated_project.tasks[i].timeline.last_updated = this.populated_project.tasks[i].timeline.last_updated?.slice(0, 19);
            }

            this.task_data = this.populated_project.tasks;
        },

        async refresh() {
            const res = await fetch(`http://localhost:8080/projects/${this.populated_project._id}`);
            const project = await res.json();

            this.populated_project = project;
            this.populated_project.managerName = this.populated_project.manager.first + " " + this.populated_project.manager.last;
        },
    },
    async beforeRouteEnter(to, from, next) {
        const projectRes = await fetch(`http://localhost:8080/projects/${to.params.id}`);
        const project = await projectRes.json();

        next(function (vm) {
            return vm.setData(project);
        });
    }
};
</script>

<style>
.form {
    width: 100%;
    border-radius: 16px;
    box-shadow: 0px 30px 40px -20px #acabab;
}

.task-header {
    display: flex;
    justify-content: space-between;
    padding-top: 2em
}

.task-btn {
    background: #009eff;
    color: white;
    margin: 0;
    padding: 10px;
    border: 1px solid #343a40;
    border-radius: 10px;
    font-size: 16px;
    font-family: 'Poppins', sans-serif;
    font-weight: bold;
    letter-spacing: 0.75px;
    text-transform: uppercase;
    cursor: pointer;
}

.task-btn:hover {
    color: white;
    background: #333333;
    font-weight: normal;
}

.task-container {
    display: flex;
    justify-content: space-evenly;
}

.task-block {
    width: 24%;
}

h2 {
    font-size: 2em;
}

h3 {
    color: #343a40;
    font-style: italic;
}

.task-card {
    background-color: white;
    border-radius: 24px;
    box-shadow: 0px 30px 40px -20px #acabab;
    margin-bottom: 2em;
}

.task-card .content {
    cursor: default;
    min-height: 175px;
    padding: 2px 1em;
}

.assigned {
    border-left: 6px solid blue;
}

.in-progress {
    border-left: 6px solid #eed202;
}

.in-review {
    border-left: 6px solid orange;
}

.completed {
    border-left: 6px solid lightgreen;
}

.title {
    color: #343a40;
    font-size: 1.5em;
    font-family: 'Poppins', sans-serif;
    font-weight: bold;
    display: flex;
    justify-content: space-between;
    margin: 0;
}

.icons {
    cursor: pointer;
    font-size: 1.5em;
    font-weight: bold;
    margin: 0 0.5em;
}

#trash-can:hover {
    color: #b73e3e;
}

p.details {
    color: lightslategray;
}

hr {
    border-top: 1px dashed black;
}

.priority {
    font-weight: bold;
    text-transform: uppercase;
    letter-spacing: 0.5px;
}
</style>