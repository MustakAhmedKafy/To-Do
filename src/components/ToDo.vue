<script setup>
import { ref } from "vue";
const tasks = ref([
  { name: "Movie", status: "Pending", priority: "1" },
  { name: "Coding", status: "Done", priority: "2" },
  { name: "Coding", status: "Paused", priority: "3" },
  { name: "Coding", status: "Not Started", priority: "3" },
  { name: "Walking", status: "Not Started", priority: "1" },
]);

const priorities = ref({
  1: "High",
  2: "Medium",
  3: "Low",
});

const newTaskName = ref("");
const newPriority = ref("");

const editingTaskName = ref("");
const editingPriority = ref("");
const editingIndex = ref(null);

const isModalOpen = ref(false);

// task add
const addTask = () => {
  tasks.value.push({
    name: newTaskName.value,
    status: "Not Started", // Add the status property
    priority: newPriority.value,
  });
  resetForm();
};

// Define a function to get the status class based on the task status
const getStatusClass = (status) => {
  return status === "Pending"
    ? "badge text-bg-warning"
    : status === "Not Started"
    ? "badge text-bg-dark"
    : status === "Paused"
    ? "badge text-bg-secondary"
    : "badge text-bg-success";
};

// Priority
const getPriorityClass = (priority) => {
  return priority === "1"
    ? "badge text-bg-danger"
    : priority === "2"
    ? "badge text-bg-warning"
    : "badge text-bg-success";
};

// Define a function to edit a task
const editTask = (index) => {
  editingIndex.value = index;
  editingTaskName.value = tasks.value[index].name;
  editingPriority.value = tasks.value[index].priority;
  isModalOpen.value = true; // Open the modal
};

const saveEditedTask = () => {
  const index = editingIndex.value;
  tasks.value[index].name = editingTaskName.value;
  tasks.value[index].priority = editingPriority.value;
  isModalOpen.value = false; // Close the modal
};

// Define a function to delete a task
const deleteTask = (index) => {
  console.log("Delete task:", tasks.value[index]);
  tasks.value.splice(index, 1);
};

// update task status
const updateStatus = (index, newStatus) => {
  tasks.value[index].status = newStatus;
};
</script>

<template>
  <section class="banner text-center text-white">
    <h1>Todo List</h1>
  </section>

  <!-- add task start -->
  <div class="container">
    <div class="card text-center d-flex align-items-center p-3 border-0 shadow">
      <input
        type="text"
        class="form-control w-50 border-0 rounded-pill"
        placeholder="What would you like to do?"
        aria-describedby="basic-addon1"
        v-model="newTaskName"
      />
      <select
        v-model="newPriority"
        class="form-select mt-3 w-50 rounded-pill"
        aria-label="Default select example"
      >
        <option v-for="(priority, label) in priorities" :value="label">
          {{ priority }}
        </option>
      </select>
      <button @click="addTask" class="btn btn-primary w-25 mt-3">Add</button>
    </div>
  </div>
  <!-- add task end -->

  <div class="container py-5 my-5">
    <div class="row justify-content-center">
      <div class="col-md-6">
        <table class="table table-striped text-center">
          <thead>
            <tr>
              <th scope="col">List</th>
              <th scope="col">Status</th>
              <th scope="col">Priority</th>
              <th scope="col">Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(task, index) in tasks" :key="index">
              <th scope="row">
                {{ task.name }}
                <p class="updateWork">
                  <button
                    v-if="task.status !== 'Done'"
                    class="badge text-bg-primary"
                    @click="updateStatus(index, 'Pending')"
                  >
                    Start
                  </button>
                  <button
                    v-if="task.status !== 'Done'"
                    class="badge text-bg-secondary ms-2"
                    @click="updateStatus(index, 'Paused')"
                  >
                    Paused
                  </button>
                  <button
                    v-if="task.status === 'Done'"
                    class="badge text-bg-success ms-2 disabled"
                    disabled
                  >
                    Done
                  </button>
                  <button
                    v-if="task.status !== 'Done'"
                    class="badge text-bg-success ms-2"
                    @click="updateStatus(index, 'Done')"
                  >
                    End Task
                  </button>
                </p>
              </th>
              <!-- status -->
              <td>
                <span :class="getStatusClass(task.status)">{{
                  task.status
                }}</span>
              </td>
              <!-- priority -->
              <td>
                <span :class="getPriorityClass(task.priority)">{{
                  priorities[task.priority]
                }}</span>
              </td>

              <!-- action -->
              <td>
                <span
                  type="button"
                  class="text-info"
                  data-bs-toggle="modal"
                  data-bs-target="#staticBackdrop"
                  @click="editTask(index)"
                >
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="20"
                    height="20"
                    fill="currentColor"
                    class="bi bi-pencil-square"
                    viewBox="0 0 16 16"
                  >
                    <path
                      d="M15.502 1.94a.5.5 0 0 1 0 .706L14.459 3.69l-2-2L13.502.646a.5.5 0 0 1 .707 0l1.293 1.293zm-1.75 2.456-2-2L4.939 9.21a.5.5 0 0 0-.121.196l-.805 2.414a.25.25 0 0 0 .316.316l2.414-.805a.5.5 0 0 0 .196-.12l6.813-6.814z"
                    />
                    <path
                      fill-rule="evenodd"
                      d="M1 13.5A1.5 1.5 0 0 0 2.5 15h11a1.5 1.5 0 0 0 1.5-1.5v-6a.5.5 0 0 0-1 0v6a.5.5 0 0 1-.5.5h-11a.5.5 0 0 1-.5-.5v-11a.5.5 0 0 1 .5-.5H9a.5.5 0 0 0 0-1H2.5A1.5 1.5 0 0 0 1 2.5z"
                    />
                  </svg>
                </span>

                <span class="ps-2 text-danger" @click="deleteTask(index)">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="20"
                    height="20"
                    fill="currentColor"
                    class="bi bi-trash"
                    viewBox="0 0 16 16"
                  >
                    <path
                      d="M5.5 5.5A.5.5 0 0 1 6 6v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5m2.5 0a.5.5 0 0 1 .5.5v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5m3 .5a.5.5 0 0 0-1 0v6a.5.5 0 0 0 1 0z"
                    />
                    <path
                      d="M14.5 3a1 1 0 0 1-1 1H13v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V4h-.5a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1H6a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1h3.5a1 1 0 0 1 1 1zM4.118 4 4 4.059V13a1 1 0 0 0 1 1h6a1 1 0 0 0 1-1V4.059L11.882 4zM2.5 3h11V2h-11z"
                    />
                  </svg>
                </span>
              </td>
            </tr>
          </tbody>
        </table>
        <!--edit task Modal start-->
        <div
          class="modal fade"
          id="staticBackdrop"
          v-bind:class="{ show: isModalOpen }"
          data-bs-backdrop="static"
          data-bs-keyboard="false"
          tabindex="-1"
          aria-labelledby="staticBackdropLabel"
          aria-hidden="true"
        >
          <div class="modal-dialog">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title" id="staticBackdropLabel">Edit Task</h5>
                <button
                  type="button"
                  class="btn-close"
                  data-bs-dismiss="modal"
                  aria-label="Close"
                ></button>
              </div>
              <div class="modal-body">
                <!-- edit task  -->
                <input
                  type="text"
                  class="form-control border-0 rounded-pill"
                  placeholder="What would you like to do?"
                  aria-describedby="basic-addon1"
                  v-model="editingTaskName"
                />
                <select
                  v-model="editingPriority"
                  class="form-select mt-3 rounded-pill"
                  aria-label="Default select example"
                >
                  <option
                    v-for="(priority, label) in priorities"
                    :value="label"
                  >
                    {{ priority }}
                  </option>
                </select>
              </div>
              <div class="modal-footer">
                <button
                  type="button"
                  class="btn btn-secondary"
                  data-bs-dismiss="modal"
                >
                  Close
                </button>
                <button
                  type="button"
                  class="btn btn-primary"
                  @click="saveEditedTask"
                >
                  Edit Task
                </button>
              </div>
            </div>
          </div>
        </div>
        <!--edit task Modal end-->
      </div>
    </div>
  </div>
</template>

<style scoped>
.banner {
  background: linear-gradient(
    90deg,
    rgba(57, 36, 103, 1) 0%,
    rgba(93, 53, 135, 1) 10%,
    rgba(163, 103, 177, 1) 100%
  );
  padding: 100px;
}
.form-control {
  /* background-color: #a4a5a338 !important; */
  border: 1px solid #a4a5a3 !important;
}

.form-control:focus {
  box-shadow: none;
  border: 1px solid rgba(163, 103, 177, 1) !important;
}
.card {
  margin-top: -65px;
}
tr {
  vertical-align: middle;
}
.updateWork {
  button {
    border: 0px;
  }
}
</style>
