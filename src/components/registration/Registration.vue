<template>
  <section class="hero">
    <div class="hero-body">
      <div class="container has-text-centered">
        <h1 class="title">ユーザー登録</h1>
        <div class="box column is-4 is-offset-4">
          <div v-if="!verifying && !verified" class="notification is-danger">
            招待リンクが無効です。
          </div>
          <form v-else-if="verified" v-on:submit.prevent="onSubmit">
            <div class="field">
              <user-config v-model="user" @validated="onValidated" :disabled="sending"/>
            </div>
            <div class="field">
              <error-notification :error="error"/>
            </div>
            <div class="field">
              <button
                class="button"
                :disabled="!isValidUserDate"
                :class="{ 'is-loading': sending }"
                >
                登録
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { mapMutations } from 'vuex';
import api from '@/api';
import ErrorNotification from '../common/ErrorNotification';
import UserConfig from '../user/UserConfig';

export default {
  name: 'Registration',
  components: {
    ErrorNotification,
    UserConfig,
  },
  data() {
    return {
      user: {
        email: null,
        displayName: null,
        name: null,
        password: null,
      },
      verifying: true,
      verified: false,
      isValidUserDate: false,
      sending: false,
      error: null,
    };
  },
  computed: {
    token() {
      return this.$route.params.token;
    },
  },
  async created() {
    this.verifying = true;
    try {
      const res = (await api.verifyEmailConfirmationToken(this.token)).data;
      this.user.email = res.email;
      this.verified = true;
    } catch (e) {
      if (!e.response || e.response.status !== 404) {
        this.error = e;
      }
    } finally {
      this.verifying = false;
    }
  },
  methods: {
    ...mapMutations('koneko', [
      'setSessionID',
    ]),
    onValidated(isValid) {
      this.isValidUserDate = isValid;
    },
    async onSubmit() {
      this.sending = true;
      try {
        const res = (await api.registerUser(this.user, this.token)).data;
        this.setSessionID(res.token);
        this.$router.push('/');
      } catch (e) {
        this.error = e;
        this.sending = false;
      }
    },
  },
};
</script>
