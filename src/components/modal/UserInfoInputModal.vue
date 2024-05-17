
<template>
  <Modal ref="modalRef">
    <template #title>
      Add new user
    </template>
    <template #body>
      <AlertDanger :message="errorMessage"/>
      <AlertSuccess :message="successMessage"/>
      <div class="container text-end">
        <div class="row mb-3">
          <div class="col align-content-md-center">
            User role:
          </div>
          <div class="col">
            <UserRoleDropdown/>
          </div>
          </div>
        <div class="row mb-3">
          <div class="col align-content-md-center">
            User name:
          </div>
          <div class="col">
            <input v-model="userInfo.username" type="text" class="form-control" id="username">
          </div>
        </div>
        <div class="row mb-3">
          <div class="col align-content-md-center">
            E-mail:
          </div>
          <div class="col">
            <input v-model="userInfo.email" type="email" class="form-control" id="email">
          </div>
        </div>
        <div class="row mb-3">
          <div class="col align-content-md-center">
            Password:
          </div>
          <div class="col">
            <input v-model="userInfo.password" type="password" class="form-control" id="password">
          </div>
        </div>
        <div class="row mb-3">
          <div class="col align-content-md-center">
            Confirm password:
          </div>
          <div class="col">
            <input v-model="userInfo.passwordConfirmation" type="password" class="form-control" id="passwordConfirmation">
          </div>
        </div>
      </div>
    </template>

    <template #buttons>

        <button @click="executeAddUser" type="button" class="btn btn-success">Add user</button>

    </template>

  </Modal>
</template>

<script>
import Modal from "@/components/modal/Modal.vue";
import UserRoleDropdown from "@/components/dropdown/UserRoleDropdown.vue";
import router from "@/router";
import AlertDanger from "@/components/alert/AlertDanger.vue";
import AlertSuccess from "@/components/alert/AlertSuccess.vue";

export default {
  name: "UserInfoInputModal",
  components: {AlertSuccess, AlertDanger, UserRoleDropdown, Modal},
  data() {
    return {
      successMessage:'',
      errorMessage:'',
      message:'',

      userInfo: {
      roleName:'',
      username: '',
      email: '',
      password: '',
      passwordConfirmation: ''
      }
    }
  },
  methods: {
    executeAddUser() {
      if (this.allFieldsHaveInput() && this.passwordInputConfirmed()) {
        this.sendPostUserRequest()
        this.handlePostUserResponse()
      } else if (this.allFieldsHaveInput()) {
        this.displayPasswordEqualityAlert()
      } else {
        this.displayAllFieldsRequiredAlert()
      }
    },

    sendPostUserRequest() {
      this.$http.post("/users",this.userInfo)
          .then(() => this.handlePostUserResponse())
          .catch(error => {
            this.errorResponse = error.response.data

          })
    },

      allFieldsHaveInput() {
        return this.userInfo.username !== '' &&
            this.userInfo.email !== '' &&
            this.userInfo.password !== '' &&
            this.userInfo.passwordConfirmation !== ''
      },

    passwordInputConfirmed() {
      if (this.userInfo.password === this.userInfo.passwordConfirmation) {
        return true
      }
    },


    displayAllFieldsRequiredAlert() {
      this.message = "Fill in all fields";
      setTimeout(this.resetMessage, 4000);
    },

    displayPasswordEqualityAlert() {
      this.message = "Passwords are not identical";
      setTimeout(this.resetMessage, 4000);
    },

    resetMessage() {
      this.message = ''
    },

    handlePostUserResponse() {
      this.successMessage = 'Uus Kasutaja "' + this.userInfo.username + '" lisatud süsteemi'
      setTimeout(this.$refs.modalRef.closeModal, 3000)
      setTimeout(this.resetMessage, 3000)
    }
  }
}
</script>