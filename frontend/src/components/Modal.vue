<template>
    <div class="modal-container">
        <div class="modal">
            <span class="cross" @click="closeModal">&#x274C;</span>
            <ProjectForm style="padding-top: 2.5em;" v-if="project" v-bind:project="project" v-bind:available_users="available_users" @closeModal="closeModal" @refresh="refresh" />
            <TaskForm v-if="task" style="padding-top: 2.5em;" v-bind:task="task" v-bind:available_users="available_users" v-bind:pID="pID" @closeModal="closeModal" @refresh="refresh" />
            <button class="close-modal" @click="closeModal">Close</button>
        </div>
    </div>
</template>

<script>
import ProjectForm from './ProjectForm.vue';
import TaskForm from './TaskForm.vue';

export default {
    name: 'ProjectModal',
    components: {
        ProjectForm,
        TaskForm
    },
    props: {
        project: Object,
        task: Object,
        pID: String,
        available_users: Array
    },
    methods: {
        closeModal() {
            this.$emit('closeModal');
        },
        refresh() {
            this.$emit('refresh');
        }
    }
};
</script>

<style>
.cross {
    top: 0;
    right: 0;
    float: right;
    padding-top: 12px;
    padding-right: 12px;
}

.cross:hover {
    cursor: pointer;
}

.modal-container {
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    position: fixed;
    background-color: #000000bf;
    backdrop-filter: blur(4px);
}

.modal {
    background-color: white;
    border: 1px solid white;
    border-radius: 24px;
    box-shadow: 0px 30px 40px -20px black;
    width: 700px;
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

.close-modal {
    background: white;
    border: 1px solid black;
    color: black;
    border-radius: 16px;
    cursor: pointer;
    font-size: 16px;
    font-family: 'Poppins', sans-serif;
    font-weight: bold;
    display: block;
    width: 100px;
    padding: 8px;
    margin: 20px auto;
}

.close-modal:hover {
    background: #dddddd;
}
</style>