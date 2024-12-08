<template>
    <form>
        <div>
            <label>Task Name</label>
            <input type="text" name="name" v-model="task.name"
                :disabled="['in-progress', 'in-review', 'completed'].includes(task.status)" required />
        </div>
        <div>
            <label>Details</label>
            <textarea type="text" name="details" v-model="task.details"
                :disabled="['in-progress', 'in-review', 'completed'].includes(task.status)" required></textarea>
        </div>
        <div style="display: flex; justify-content: flex-start;">
            <div>
                <label>Select Priority</label>
                <br />
                <select class="dropdown" name="priority" v-model="task.priority"
                    :disabled="['in-progress', 'in-review', 'completed'].includes(task.status)" required>
                    <option value="low">Low</option>
                    <option value="medium">Medium</option>
                    <option value="high">High</option>
                </select>
            </div>
            <div style="margin-left: 2em" v-show="task.status">
                <label>Status</label>
                <br />
                <select class="dropdown" name="status" v-model="task.status">
                    <option value="assigned">Assigned</option>
                    <option value="in-progress">In-Progress</option>
                    <option value="in-review">In-Review</option>
                    <option value="completed">Completed</option>
                </select>
            </div>
            <div style="margin-left: 2em">
                <label>User</label>
                <br />
                <select class="dropdown" v-model="selected_user" style="width: 200px" v-if="getTaskUser(task.user)"
                    :disabled="['assigned', 'in-progress', 'in-review', 'completed'].includes(task.status)">
                    <option v-for="user in available_users" :value="user._id">
                        {{ user.name }}
                    </option>
                </select>
                <select class="dropdown" v-else style="width: 200px"
                    :disabled="['assigned', 'in-progress', 'in-review', 'completed'].includes(task.status)">
                    <option v-for="user in available_users" :value="user._id">
                        {{ user.name }}
                    </option>
                </select>
            </div>
        </div>
        <hr style="margin: 1em 0;" />
        <h3 style="font-style: italic">Timeline</h3>
        <div v-if="task.timeline" v-show="task.status">
            <label>Assigned</label>
            <input type="datetime-local" name="assigned" v-model="task.timeline.assigned" disabled />
        </div>
        <div v-if="task.timeline">
            <label>Due</label>
            <input type="datetime-local" name="due" v-model="task.timeline.due"
                :disabled="['in-progress', 'in-review', 'completed'].includes(task.status)" required />
        </div>
        <div v-if="task.timeline" v-show="task.status">
            <label>Last Updated</label>
            <input type="datetime-local" name="last_updated" v-model="task.timeline.last_updated" disabled />
        </div>
        <div>
            <input type="button" v-if="task._id" @click="updateTask" value="Update &#8634;" />
            <!-- change type to submit -->
            <input type="button" v-else @click="addTask(pID)" value="Add Task &#43;" />
        </div>
    </form>
</template>

<script>
export default {
    name: 'TaskForm',
    props: {
        task: Object,
        available_users: Array,
        pID: String,
    },
    data() {
        return {
            selected_user: '',
        }
    },
    methods: {
        async addTask(pID) {
            this.task.timeline.assigned = new Date().toISOString();

            const data = {
                ...this.task,
                status: 'assigned',
                project: pID,
            };

            const res = await fetch(`http://localhost:8080/tasks`, {
                method: 'POST',
                mode: 'cors',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(data)
            });

            if (res.ok) {
                this.$emit('closeModal');
                this.$emit('refresh');
            } else {
                this.handleError();
            }
        },

        async updateTask() {
            const data = {
                name: this.task.name,
                details: this.task.details,
                priority: this.task.priority,
                status: this.task.status,
                timeline: {
                    assigned: this.task.timeline.assigned,
                    due: this.task.timeline.due,
                }
            };

            const res = await fetch(`http://localhost:8080/tasks/${this.task._id}`, {
                method: 'PUT',
                mode: 'cors',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(data)
            });

            if (res.ok) {
                this.$emit('closeModal');
                this.$emit('refresh');
            } else {
                this.handleError();
            }
        },

        getTaskUser(user) {
            this.selected_user = user;
            return this.selected_user;
        }
    }
};
</script>

<style>
.dropdown {
    width: 120px;
    border-radius: 8px;
    margin-top: 1em;
    padding: 6px 0;
    font-size: 16px;
}

form {
    margin: 0px auto;
    padding: 10px;
    width: 90%;
    height: auto;
    background: white;
}

form label {
    font-family: 'Poppins', sans-serif;
    font-size: 16px;
    color: #343a40;
}

form input,
form textarea {
    margin: 10px 0;
    padding: 10px 10px;
    width: 95%;
    border: 1px solid #333333;
    border-radius: 5px;
    display: block;
}

textarea {
    font-family: 'Poppins', sans-serif;
}

input[type="submit"],
input[type="button"] {
    background: orange;
    color: black;
    border-radius: 10px;
    font-size: 16px;
    font-family: 'Poppins', sans-serif;
    font-weight: bold;
    display: inline;
    width: 120px;
    margin-top: 1em;
    margin-right: 10px;
    letter-spacing: 0.5px;
    text-transform: uppercase;
    cursor: pointer;
}

input[type="submit"]:hover,
input[type="button"]:hover {
    background: #eed202;
}

input[type="datetime-local"] {
    font-family: 'Poppins', sans-serif;
    margin-bottom: 1em;
    font-size: 16px;
}
</style>