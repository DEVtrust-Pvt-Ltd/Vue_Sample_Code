<script>
export default {
  layout: "noheaderfooter",
  auth: false,
  data() {
    return {
      formValues: {},
      formErrors: [],
      inputErrors: {},
      message: "",
      success: false,
      enabled: true,
    };
  },
  methods: {
    async submitHandler(data) {
      var self = this;
      self.inputErrors = {};
      await this.$axios
        .post("/v1/auth/register", data)
        .then(function (response) {
          self.success = true;
          self.message =
            "Activate your account email sent. Please check your email to complete account activation.";
          setTimeout(function () {
            self.checkAccountStatus();
          }, 1000 * 60 * 60);
        })
    },
      errorCaptured(err, vm, info) {
    this.err = err;
    this.vm = vm;
    this.info = info;
    return !this.stopPropagation;
  },
    checkAccountStatus() {
      var self = this;
      this.$axios
        .post("/v1/auth/status", this.formValues)
        .then(function (res) {
          if (res.data.status != 1) {
            self.message =
              "Account could not be created. Activation Key is expired";
          }
        })
        .catch(function (error) {});
    },
  },

  mounted() {
    setTimeout(function () {
      document.body.classList.remove("Loading");
    }, 0);
  },
};
</script>

<template>
  <div class="form--section">
    <div class="privacy-modal">
    </div>
    <div class="form-bg"></div>
    <div class="signup--form">
      <div class="spn-logo">
        <NuxtLink to="/">
          <img src="../static/spn-logo.svg" alt="" />
        </NuxtLink>
      </div>

      <FormulateForm
        v-model="formValues"
        class="login-form"
        @submit="submitHandler"
        #default="{ isLoading }"
        v-if="!success && enabled"
        :form-errors="formErrors"
        :errors="inputErrors"
      >
        <h2 class="form-title">Create a new account</h2>
        <FormulateInput
          name="first_name"
          type="text"
          label="First Name"
          placeholder="First Name"
          validation="required"
        />
        <FormulateInput
          name="last_name"
          type="text"
          label="Last Name"
          placeholder="Last Name"
          validation="required"
        />
        <FormulateInput
          name="email"
          type="email"
          label="Email address"
          placeholder="Email address"
          validation="required|email"
        />
        <div class="double-wide">
          <FormulateInput
            name="password"
            type="password"
            label="Password"
            placeholder="Your password"
            validation="required"
          />
          <FormulateInput
            name="password_confirm"
            type="password"
            label="Confirm your password"
            placeholder="Confirm password"
            validation="required|confirm"
            validation-name="Confirmation"
          />
        </div>

        <div class="policy-checkbox">
          <FormulateInput
            type="checkbox"
            validation="required"
            validation-name="Please accept the terms of the privacy policy"
          >
          </FormulateInput>
        </div>

        <div class="actions">
          <FormulateInput
            class="form-btn"
            type="submit"
            :label="isLoading ? 'Loading...' : 'Create Account'"
            :disabled="isLoading"
          />
          <div class="form-link success" v-if="success"></div>
        </div>
      </FormulateForm>
      <div class="form-link" v-if="success">
        {{ message }}
      </div>
      <div class="form-link" v-if="!enabled">Site is under construction</div>
      <div class="form-link" v-if="!success">
        Have an account?
        <NuxtLink class="hover-link" to="/login">Sign In</NuxtLink>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
@import "../styles/main.scss";
</style>
