<template>

  <div id="signup-page">

    <ui-toolbar type="colored" textColor="white">
      <template slot="icon">
        <img id="logo" src="../img/instant-school-logo.png" />
      </template>
      <template slot="brand">
        Instant Schools
      </template>
      <div slot="actions">
        <router-link id="login" :to="signInPage">
          <span>Log In</span>
        </router-link>
      </div>
    </ui-toolbar>

    <form class="signup-form" ref="form" @submit.prevent="signUp">
      <ui-alert type="error" @dismiss="resetSignUpState" v-if="errorCode">
        {{errorMessage}}
      </ui-alert>

      <h1 class="signup-title">{{ $tr('createAccount') }}</h1>

      <ui-textbox
        :placeholder="$tr('enterName')"
        :label="$tr('name')"
        :aria-label="$tr('name')"
        v-model="name"
        autocomplete="name"
        autofocus
        required
        id="name"
        type="text" />

      <ui-textbox
        :placeholder="$tr('enterUsername')"
        :label="$tr('username')"
        :aria-label="$tr('username')"
        :invalid="usernameError"
        v-model="username"
        autocomplete="username"
        required
        id="username"
        type="text" />

      <ui-textbox
        id="password"
        type="password"
        :placeholder="$tr('enterPassword')"
        :aria-label="$tr('password')"
        :label="$tr('password')"
        v-model="password"
        autocomplete="new-password"
        required />

      <ui-textbox
        id="confirmed-password"
        type="password"
        :placeholder="$tr('confirmPassword')"
        :aria-label="$tr('confirmPassword')"
        :label="$tr('confirmPassword')"
        :invalid="!passwordsMatch"
        :error="passwordError "
        v-model="confirmed_password"
        autocomplete="new-password"
        required />


      <div class="terms">
        <small>
          <p>
            1. Terms of Service: By accessing the application Kolibri you are
            agreeing to be bound by these terms of service, all applicable laws and
            regulations, and agree that you are responsible for compliance with any
            applicable local laws.
            <br>
            <em>
              Note: Complete Terms to follow preliminary testing period.
            </em>
          </p>
          <p>
            2. Privacy policy: By accessing the application Kolibri you acknowledge
            the use of this platform and how data are collected, used, and shared
            between Learning Equality and Vodafone Foundation.
            <br>
            <em>
              Note: Complete Privacy Notice to follow preliminary testing period.
            </em>
          </p>
        </small>
      </div>

      <ui-checkbox v-model="termsAgreement" required>
        {{$tr('termsAgreement')}}
      </ui-checkbox>

      <icon-button :disabled="canSubmit" id="submit" :primary="true" text="Finish" type="submit" />

    </form>

  </div>

</template>


<script>

  const actions = require('../../actions');
  const PageNames = require('../../state/constants').PageNames;

  module.exports = {
    name: 'Sign-Up-Page',
    $trNameSpace: 'signUpPage',
    $trs: {
      createAccount: 'Create an Account',
      name: 'Name',
      enterName: 'Enter Name',
      username: 'Phone Number',
      enterUsername: 'Enter Phone Number',
      password: 'Password',
      enterPassword: 'Enter Password',
      confirmPassword: 'Confirm Password',
      passwordMatchError: 'Passwords do not match',
      genericError: 'Something went wrong during sign up!',
      termsAgreement: 'I agree to the terms of service & privacy policy',
    },
    components: {
      'icon-button': require('kolibri.coreVue.components.iconButton'),
      'ui-alert': require('keen-ui/src/UiAlert'),
      'ui-textbox': require('keen-ui/src/UiTextbox'),
      'ui-toolbar': require('keen-ui/src/UiToolbar'),
      'ui-checkbox': require('keen-ui/src/UiCheckbox'),
    },
    data: () => ({
      name: '',
      username: '',
      password: '',
      confirmed_password: '',
      termsAgreement: false,
    }),
    computed: {
      signInPage() {
        return { name: PageNames.SIGN_IN };
      },
      passwordsMatch() {
        // make sure both fields are populated
        if (this.password && this.confirmed_password) {
          return this.password === this.confirmed_password;
        }
        return true;
      },
      passwordError() {
        if (this.passwordsMatch) {
          return '';
        }
        return this.$tr('passwordMatchError');
      },
      usernameError() {
        return this.errorCode === 400;
      },
      allFieldsPopulated() {
        return !(this.name && this.username && this.password && this.confirmed_password);
      },
      canSubmit() {
        return !this.termsAgreement || this.allFieldsPopulated || !this.passwordsMatch || this.busy;
      },
      errorMessage() {
        return this.backendErrorMessage || this.$tr('genericError');
      },
    },
    methods: {
      signUp() {
        this.signUpAction({
          full_name: this.name,
          username: this.username,
          password: this.password,
        });
      },
    },
    vuex: {
      getters: {
        session: state => state.core.session,
        errorCode: state => state.pageState.errorCode,
        busy: state => state.pageState.busy,
        backendErrorMessage: state => state.pageState.errorMessage,
      },
      actions: {
        signUpAction: actions.signUp,
        resetSignUpState: actions.resetSignUpState,
      },
    },
  };

</script>


<style lang="stylus" scoped>

  @require '~kolibri.styles.definitions'
  $iphone-5-width = 320px
  $vertical-page-margin = 100px
  $logo-size = (1.64 * 1.125)rem
  $logo-margin = (0.38 * $logo-size)rem

  // component, highest level
  #signup-page
    width: 100%
    height: 100%
    overflow-y: auto

  // Action Bar
  #logo
    // 1.63 * font height
    height: $logo-size
    display: inline-block
    margin-left: $logo-margin

  #login
    margin-right: 1em
    color: white
    text-decoration: none

  // Form
  .signup-title
    text-align: center

  .signup-form
    margin-top: $vertical-page-margin
    margin-left: auto
    margin-right: auto
    width: ($iphone-5-width - 20)px

  .terms
    background-color: $core-bg-light
    color: $core-text-annotation
    height: 6em
    overflow-y: scroll
    padding: 0.5em
    margin-bottom: 1em
    p
      margin-top: 0

  #submit
    width: 90%
    display: block
    margin-left: auto
    margin-right: auto

    margin-top: $vertical-page-margin
    margin-bottom: $vertical-page-margin

</style>
