<template>
  <b-card-body>
    <b-card-title>{{ $t(`view.request-password-reset.title`) }}</b-card-title>
    <p
      v-if="done"
      class="request-success"
    >
      {{ $t('view.request-password-reset.password-request-sent') }}
    </p>
    <b-form
      v-else
      class="request-form"
      @submit.prevent="requestPasswordReset"
    >
      <div
        v-if="error"
        class="text-danger mb-1 error"
      >
        {{ $t('general.error-tpl', { error: parseError(error) }) }}
      </div>
      <b-input-group>
        <b-input-group-prepend>
          <span class="input-group-text bg-primary text-white">
            <font-awesome-icon :icon="['fas', 'at']" />
          </span>
        </b-input-group-prepend>
        <b-form-input
          v-model="form.email"
          type="email"
          name="email"
          :label="$t('view.request-password-reset.form.email.label\'')"
          :placeholder="$t('view.request-password-reset.form.email.placeholder')"
          required
          autocomplete="email"
        />
      </b-input-group>
      <b-form-group class="text-right">
        <b-button
          type="submit"
          variant="primary"
          class="mt-2"
          :disabled="disabledSubmit"
        >
          {{ $t('view.request-password-reset.form.send') }}
        </b-button>
      </b-form-group>
    </b-form>
    <div class="text-center">
      <router-link :to="{ name: 'auth:login' }">
        {{ $t('link.login-cta' ) }}
      </router-link>
    </div>
  </b-card-body>
</template>

<script>

export default {
  name: 'RequestPasswordReset',

  props: {
    internalPasswordResetEnabled: {
      type: Boolean,
      required: true,
    },
  },

  data () {
    return {
      processing: false,
      done: false,

      error: null,

      form: {
        email: '',
      },
    }
  },

  computed: {
    disabledSubmit () {
      return this.processing
    },
  },

  created () {
    if (!this.internalPasswordResetEnabled) {
      this.$router.push({ name: 'auth:login' })
      return
    }

    this.gotoProfileIfAuthenticated()
  },

  methods: {
    requestPasswordReset () {
      if (!this.internalPasswordResetEnabled) {
        return false
      }

      this.error = null
      this.processing = true
      this.done = false

      this.$SystemAPI.authInternalRequestPasswordReset(this.form).then(r => {
        this.done = true
      }).catch(({ message } = {}) => {
        this.error = message
      }).finally(() => {
        this.processing = false
      })
    },
  },
}
</script>
