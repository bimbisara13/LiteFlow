<template>
    <form>
        <div>
            <label>Project Name</label>
            <input type="text" name="name" v-model="project.name" />
        </div>
        <div>
            <label>Description</label>
            <textarea type="text" name="description" v-model="project.description"></textarea>
        </div>
        <div>
            <label>Repository</label>
            <input type="text" name="repository" v-model="project.repository" />
        </div>
        <div v-if="project.manager">
            <label>Manager</label>
            <input type="text" name="manager" v-model="project.managerName" disabled />
        </div>
        <div v-if="(project._id && available_users)">
            <label>Users</label>
            <br />
            <select multiple v-model="selected_users" style="width: 50%" class="dropdown">
                <option v-for="user in available_users" :value="user._id">{{ user.name }}</option>
            </select>
        </div>
        <div>
            <input type="button" @click="updateProject" value="Update &#8634;" />
        </div>
    </form>
</template>

<script>
export default {
    name: 'ProjectForm',
    props: {
        project: Object,
        available_users: Array
    },
    data() {
        return {
            selected_manager: '',
            selected_users: []
        }
    },
    methods: {
        async updateProject() {
            const data = {
                name: this.project.name,
                description: this.project.description,
                repository: this.project.repository,
            };

            const res = await fetch(`http://localhost:8080/projects/${this.project._id}`, {
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
        }
    },
    async created() {
        if (this.project._id) {
            this.selected_manager = this.project.manager._id
        }
    }
};
</script>

<style>
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

input[type="button"]:hover {
    background: #eed202;
}
</style>