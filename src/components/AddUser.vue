<template>
  <h2>Add user</h2>
  <div class="add-user-form">
    <div class="form-container div1">
      <form>
        <div class="input-wrapper">
          <div class="form-group">
            <label for="first-name">First Name</label>
            <input type="text" v-model="firstName" id="first-name" required />
          </div>
          <div class="form-group">
            <label for="last-name">Last Name</label>
            <input type="text" v-model="lastName" id="last-name" required />
          </div>
        </div>
        <button @click="submitForm" type="submit" class="submit-btn">
          Update Details
        </button>
      </form>
    </div>
    <div class="avatar-section">
      <img
        :src="avatarPreview || defaultAvatar"
        alt="User Avatar"
        class="avatar"
      />
      <input type="file" @change="onFileChange" id="avatar-upload" />
      <label for="avatar-upload" class="change-photo-btn">
        <i class="fas fa-camera"></i> Change Photo
      </label>
    </div>
  </div>
</template>

<script setup>
import { ref, defineEmits, defineProps, onMounted } from "vue";

const props = defineProps({
  user: {
    type: Object,
    default: () => null,
  },
  isEdit: {
    type: Boolean,
    default: false,
  },
});

const emit = defineEmits(["user-added", "cancel-add", "user-updated"]);

const firstName = ref("");
const lastName = ref("");
const avatarPreview = ref(null);
const defaultAvatar = "https://via.placeholder.com/150";

onMounted(() => {
  if (props.isEdit) {
    firstName.value = props.user?.first_name || "";
    lastName.value = props.user?.last_name || "";
    avatarPreview.value = props.user?.avatar || "";
  }
});

const onFileChange = (e) => {
  const file = e.target.files[0];
  if (file) {
    avatarPreview.value = URL.createObjectURL(file);
  }
};

const submitForm = () => {
  const updatedUser = {
    id: props.user?.id || null,
    first_name: firstName.value,
    last_name: lastName.value,
    avatar: avatarPreview.value || defaultAvatar,
  };

  if (props.isEdit) {
    emit("user-updated", { updatedUser, isEdit: false });
  } else {
    emit("user-added", updatedUser);
  }

  firstName.value = "";
  lastName.value = "";
  avatarPreview.value = null;
};
</script>

<style scoped>
.add-user-form {
  display: grid;
  grid-template-columns: 2fr 1fr;
  grid-template-rows: auto;
  column-gap: 1rem;
}

h2 {
  text-align: left;
  font-size: 2rem;
  font-weight: 500;
}

.form-container {
  /* grid-area: 1 /  / 2 / 1; */
  display: flex;
  justify-content: space-between;
  background-color: #fff;
  height: 100%;
  padding: 2rem;
  order: 1;
}

form {
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  width: 100%;
  gap: 1rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  margin-bottom: 1em;
  width: 50%;
  margin-top: 1rem;
}

.form-group label {
  font-size: 1rem;
  margin-bottom: 0.5em;
}

input[type="text"] {
  padding: 0.75em;
  font-size: 1rem;
  border: 0.1rem solid #ccc;
  border-radius: 0.5rem;
}

.submit-btn {
  margin-top: 1em;
  padding: 0.75em 1.5em;
  background-color: #2d8c4e;
  color: white;
  font-size: 1rem;
  border: none;
  border-radius: 0.5rem;
  align-self: self-start;
  cursor: pointer;
}

.avatar-section {
  /* grid-area: 1 / 3 / 2 / 4; */
  /* grid-area: auto; */
  height: 100%;
  display: flex;
  order: 2;
  align-items: center;
  flex-direction: column;
  justify-content: space-between;
  background-color: #fff;
  padding: 2rem;
}

.avatar {
  width: 10rem;
  height: 10rem;
  border-radius: 50%;
  object-fit: cover;
  margin-bottom: 1em;
}

input[type="file"] {
  display: none;
}

.input-wrapper {
  display: flex;
  width: 100%;
  gap: 1.5rem;
}

.change-photo-btn {
  background-color: #fff;
  border: 0.1rem solid #ccc;
  border-radius: 0.3rem;
  text-align: center;
  cursor: pointer;
  display: block;
  transition: all 0.4s;
  padding: 0.8rem 0;
  width: 100%;
}

.change-photo-btn:hover {
  background-color: #2d8c4e;
  color: #fff;
}

/* Tablet responsiveness */
@media (max-width: 768px) {
  .add-user-form {
    grid-template-columns: 1fr;
    row-gap: 2rem;
  }

  .form-container,
  .avatar-section {
    grid-area: auto;
    width: 100%;
    padding: 0.8rem;
  }

  form {
    padding: 2rem;
  }

  .avatar-section {
    order: 1;
  }

  .form-container {
    order: 2;
  }

  .input-wrapper {
    flex-direction: column;
    gap: 0;
  }

  .form-group {
    width: 100%;
  }

  .submit-btn {
    width: 100%;
    text-align: center;
  }
}

/* Mobile responsiveness */
@media (max-width: 560px) {
  .add-user-form {
    grid-template-columns: repeat(1, 1fr);
    grid-template-rows: auto auto;
    row-gap: 2rem;
  }

  .avatar {
    width: 8rem;
    height: 8rem;
  }

  .submit-btn {
    width: 100%;
    text-align: center;
  }
}
</style>
