<template>
  <div>
    <AddUser
      v-if="showAddUserForm"
      :user="selectedUser"
      :isEdit="isEditMode"
      @user-added="addUserToList"
      @cancel-add="toggleAddUserForm"
      @user-updated="updateUserInList"
    />
    <div v-else>
      <div class="user-list">
        <h1 style="text-align: start">User list</h1>
        <div class="header">
          <div class="search-input">
            <input
              type="text"
              v-model="search"
              placeholder="Search for users..."
            />
            <i class="fas fa-search"></i>
          </div>
          <button class="add-user-btn" @click="toggleAddUserForm">
            + Add User
          </button>
        </div>

        <div class="table-wrapper">
          <table>
            <thead>
              <tr>
                <th></th>
                <th>Full Name</th>
                <th class="actions-column-header">Action</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="user in paginatedUsers" :key="user.id">
                <td style="width: 1rem">
                  <img :src="user.avatar" class="avatar" alt="User Avatar" />
                </td>
                <td>
                  <span class="fullname-wrapper">
                    {{ user.first_name }} {{ user.last_name }}
                  </span>
                </td>
                <td class="actions-column">
                  <button @click="editUser(user)" class="action-btn">
                    <i class="fa-solid fa-pen-to-square"></i>
                  </button>
                  <button @click="deleteUser(user.id)" class="action-btn">
                    <i color="grey" class="fas fa-trash"></i>
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <div class="pagination">
          <button @click="prevPage" :disabled="currentPage === 1">«</button>
          <button
            v-for="page in totalPages"
            :key="page"
            @click="goToPage(page)"
            :class="{ active: page === currentPage }"
          >
            {{ page }}
          </button>
          <button @click="nextPage" :disabled="currentPage === totalPages">
            »
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import axios from "axios";
import AddUser from "./AddUser.vue";

const users = ref([]);
const search = ref("");
const currentPage = ref(1);
const isEditMode = ref(false);
const selectedUser = ref(null);
const usersPerPage = 6;
const showAddUserForm = ref(false);

const fetchAllUsers = async () => {
  let page = 1;
  let totalPages = 1;
  let allUsers = [];
  try {
    while (page <= totalPages) {
      const response = await axios.get(
        `https://reqres.in/api/users?page=${page}`
      );
      allUsers = [...allUsers, ...response.data.data];
      totalPages = response.data.total_pages;
      page++;
    }
    users.value = allUsers;
  } catch (error) {
    console.error("Error fetching users:", error);
  }
};

onMounted(() => {
  fetchAllUsers();
});

const filteredUsers = computed(() => {
  return users.value.filter((user) =>
    `${user.first_name} ${user.last_name}`
      .toLowerCase()
      .includes(search.value.toLowerCase())
  );
});

const totalPages = computed(() =>
  Math.ceil(filteredUsers.value.length / usersPerPage)
);

const paginatedUsers = computed(() => {
  const start = (currentPage.value - 1) * usersPerPage;
  const end = start + usersPerPage;
  return filteredUsers.value.slice(start, end);
});

const goToPage = (page) => {
  currentPage.value = page;
};
const nextPage = () => {
  if (currentPage.value < totalPages.value) currentPage.value++;
};
const prevPage = () => {
  if (currentPage.value > 1) currentPage.value--;
};

const addUserToList = (newUser) => {
  users.value.push({
    ...newUser,
    id: users.value.length + 1,
  });
  isEditMode.value = true;
  toggleAddUserForm();
};

const toggleAddUserForm = () => {
  showAddUserForm.value = !showAddUserForm.value;
};

const editUser = (user) => {
  selectedUser.value = user;
  isEditMode.value = true;
  toggleAddUserForm();
};

const updateUserInList = (updatedUser) => {
  isEditMode.value = updatedUser.isEdit;
  const index = users.value.findIndex(
    (user) => user.id === updatedUser.updatedUser.id
  );
  if (index !== -1) {
    users.value[index] = updatedUser.updatedUser;
  }
  toggleAddUserForm();
  // isEditMode.value = true;
};

const deleteUser = (userId) => {
  const message = window.confirm("Do you want to delete the account?");
  console.log(message);
  if (message) {
    users.value = users.value.filter((user) => user.id != userId);
  }
};
</script>

<style scoped>
.user-list {
  background-color: #fff;
  padding: 2rem;
  margin: 0 auto;
  max-width: 100%;
}

.header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 1rem;
}

.actions-column,
.actions-column-header {
  text-align: end;
}

.search-input {
  position: relative;
  padding: 0 1rem;
  width: 20rem;
  display: flex;
  background-color: #f5f4f4;
  align-items: center;
}

.search-input input {
  padding-right: 1rem;
  border: none;
  background-color: transparent;
  width: 100%;
  outline: none;
  height: 100%;
}

.add-user-btn {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background-color: green;
  color: white;
  border: none;
  padding: 0.7rem 2rem;
  border-radius: 1.5rem;
  cursor: pointer;
  transition: all 0.5s;
}

.add-user-btn:hover {
  background-color: rgb(5, 166, 5);
}

.table-wrapper {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
  border: none;
}

th {
  background-color: #fff;
}

th,
td {
  padding: 1rem 3rem;
  border: none;
  text-align: left;
}

.avatar {
  width: 3rem;
  height: 3rem;
  border-radius: 50%;
}

.action-btn {
  background: none;
  border: none;
  cursor: pointer;
}

.pagination {
  display: flex;
  justify-content: flex-start;
  margin-top: 1rem;
}

.pagination button {
  padding: 0.5rem 0.8rem;
  margin: 0 0.5rem;
  border: 0.1rem solid #ddd;
  background-color: #fff;
  cursor: pointer;
}

.pagination .active {
  background-color: #008000;
  color: #fff;
}

.pagination button:disabled {
  cursor: not-allowed;
  background-color: #f0f0f0;
}

@media (max-width: 768px) {
  .header {
    flex-direction: column;
    align-items: center;
  }

  .search-input {
    width: 100%;
    margin-bottom: 1rem;
    padding: 0.7rem;
  }

  .add-user-btn {
    width: 100%;
  }

  table {
    width: 100%;
    font-size: 0.9rem;
  }

  th,
  td {
    padding: 0.5rem 1rem;
  }

  .table-wrapper {
    overflow-x: auto;
  }
}
</style>
