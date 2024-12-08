<template>
  <div class="project-list-container">
    <div class="project-block" v-for="project in projects">
      <div class="project-card">
        <div class="content">
          <span class="title">
            {{ project.name }}
          </span>
          <p class="text">
            {{ project.description }}
          </p>
          <p style="font-style: italic;">
            Repository: <span class="repo-span">{{
                project.repository
            }}</span>
          </p>
        </div>
        <div class="project-btn-container">
          <button class="btn" @click="openProjectModal(project)">Edit &#9998;</button>
          <router-link :to="`/projects/${project.id}`" class="btn">View &#8594;</router-link>
        </div>
      </div>
    </div>
  </div>

  <Modal v-bind:project="project_data" v-bind:available_users="available_users" v-show="showModal"
    @closeModal="closeModal" @refresh="refresh" />
</template>

<script>
import Modal from './Modal.vue';

export default {
  name: 'Projects',
  components: {
    Modal,
  },
  data() {
    return {
      projects: [],
      project_data: {},
      showModal: false,
      available_users: [],
    }
  },
  methods: {
    async openProjectModal(project) {
      this.showModal = true;

      const usersRes = await fetch(`http://localhost:8080/users/?project=${project.id}`);
      const users = await usersRes.json();

      this.available_users = users.map(user => {
        return { _id: user._id, name: `${user.first} ${user.last}` }
      });

      this.project_data = { ...project };
      this.project_data.managerName = this.project_data.manager.first + " " + this.project_data.manager.last;
    },


    closeModal() {
      this.showModal = false;
    },

    async refresh() {
      const res = await fetch(`http://localhost:8080/projects`);
      const body = await res.json();

      this.projects = body;
    },
  },
  async created() {
    this.refresh();
  }
};
</script>

<style>
.project-list-container {
  padding-top: 5%;
  display: grid;
  justify-content: center;
  align-content: center;
  grid-auto-columns: 1fr;
  grid-auto-flow: column;
  gap: 12px;
}

.project-block {
  margin: 2em 5px;
}

.project-card {
  border-top: 3px solid #52ffff;
  background-color: white;
  border-radius: 24px;
  box-shadow: 0px 30px 40px -20px #acabab;
}

.project-card .content {
  cursor: default;
  min-height: 200px;
  padding: 30px;
}

.project-card .title {
  color: #343a40;
  font-size: 30px;
  font-family: 'Poppins', sans-serif;
  font-weight: bold;
}

.project-card .text {
  color: lightslategray;
  font-size: 16px;
}

.project-card .project-btn-container {
  border-top: 2px solid lightgray;
  color: #FFF;
}

.repo-span {
  font-style: normal;
  color: blue;
}

.btn {
  color: #343a40;
  background-color: white;
  padding: 10px;
  margin: 10px;
  border: 1px solid #343a40;
  border-radius: 16px;
  font-size: 16px;
  font-family: 'Poppins', sans-serif;
  font-weight: bold;
  letter-spacing: 0.75px;
  text-decoration: none;
  cursor: pointer;
}

.btn:hover {
  color: white;
  background: #333333;
  font-weight: normal;
}
</style>