<template>
  <div class="container-app container-extra-padding">
    <div class="form-section">
      <label class="form-section__label text--secondary" for="emailTb">Email address</label>
      <input
        class="form-section__input"
        v-model="formData.email"
        id="emailTb"
        type="email"
        placeholder="Enter email"
      >
    </div>
    <div class="form-section">
      <label class="form-section__label text--secondary" for="passwordTb">Password</label>
      <input
        class="form-section__input"
        v-model="formData.password"
        type="password"
        id="passwordTb"
        placeholder="Password"
      >
    </div>
    <button
      @click="loginUser"
      class="button form__submit-button"
      :disabled="this.$store.getters.isPerformingAction"
    >Login</button>

    <font-awesome-icon
      id="loadingCircle"
      v-if="this.$store.getters.isPerformingAction"
      icon="spinner"
      size="3x"
      spin
      fixed-width
    ></font-awesome-icon>
  </div>
</template>

<script>
import eventbus from '../eventbus';

export default {
  data() {
    return {
      formData: {
        email: '',
        password: ''
      },
      errorCodes: {
        INVALID_PASSWORD: 'A user with that email and password was not found.',
        EMAIL_NOT_FOUND: 'A user with that email address was not found.',
        USER_DISABLED:
          'The user account has been disabled by an administrator.',
        TOO_MANY_ATTEMPTS:
          'Too many login attempts. Please try again in a few minutes.'
      }
    };
  },
  methods: {
    loginUser() {
      if (this.formData.email.length > 0 && this.formData.password.length > 0) {
        this.$store.dispatch('loginUser', this.formData);
      } else {
        eventbus.showToast('One or more required fields is empty.', 'error');
      }
    }
  },
  activated() {
    this.$store.dispatch('setTitle', 'Log in');
    eventbus.$on('login-success', message => {
      eventbus.showToast(message, 'success');
      this.$router.go(-1);
    });

    eventbus.$on('login-failure', message => {
      if (this.errorCodes.hasOwnProperty(message))
        eventbus.showToast(this.errorCodes[message], 'error');
      else eventbus.showToast(message, 'error');
    });
  }
};
</script>
