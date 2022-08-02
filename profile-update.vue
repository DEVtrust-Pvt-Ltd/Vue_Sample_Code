<script>
export default {
  layout: "noheaderfooter",
  auth: true,
  data() {
    return {
      msg_class_dynamic: "",
      formValues: {},
      message: "",
      f_name: this.$auth.user.first_name,
      l_name: this.$auth.user.last_name,
    };
  },
  methods: {
    async submitHandler(data) {
      var self = this;
      self.message = "";
      await this.$axios
        .post("/v1/auth/profile-update", data)
        .then(function (response) {
          self.message = "Your Profile Updated Successfully";
          self.msg_class_dynamic = "success_msg_green";
        })
        .catch(function (error) {
          self.message =
            "Something went wrong. Please recheck and resubmit the form.";
          self.msg_class_dynamic = "errror_msg_red";
        });
    },
  },
  mounted() {
    if (!this.$auth.loggedIn) {
      this.$router.push("login");
    }
    setTimeout(function () {
      document.body.classList.remove("Loading");
    }, 0);
  },
};
</script>

<template>
  <div class="form--section">
    <div class="form-bg"></div>
    <div class="signup--form">
      <div class="spn-logo">
      </div>
<!-- Profile update form start -->
      <FormulateForm
        v-model="formValues"
        class="login-form"
        @submit="submitHandler"
      >
        <h2 class="form-title">Update Profile</h2>

        <div class="double-wide">
          <FormulateInput
            name="first_name"
            v-model="f_name"
            type="text"
            label="First Name"
            placeholder="First Name"
            validation="required:trim"
            validation-name="First Name"
          />
          <FormulateInput
            name="last_name"
            v-model="l_name"
            type="text"
            label="Last Name"
            placeholder="Last Name"
            validation="required:trim"
            validation-name="Last Name"
          />
        </div>

        <div class="actions">
          <FormulateInput
            class="form-btn"
            type="submit"
            label="Update Account"
          />
        </div>
      </FormulateForm>
      <!-- Profile update form end -->
      <div class="form-link">
       	<NuxtLink class="hover-link" to="/">Return to Home</NuxtLink>
        <p :class="msg_class_dynamic">{{ message }}</p>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
@import "../styles/main.scss";
.errror_msg_red {
  color: red;
}
.success_msg_green {
  color: green;
}
</style>
