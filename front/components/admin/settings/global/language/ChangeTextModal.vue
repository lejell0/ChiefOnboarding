<template>
  <v-dialog
    v-model="value"
    :persistent="true"
    max-width="900"
  >
    <v-card>
      <v-card-title class="headline">
        {{ $t('settings.welcomeTextMessageHeader') }} ({{ $store.state.selectedLanguage.language }})
      </v-card-title>

      <v-card-text>
        <p>{{ $t('settings.welcomeTextMessageDescr') }}</p>
        <VTextAreaEmoji
          v-model="text"
          :label="$t('settings.textMessageLabel')"
          :personalize="true"
          :emoji="false"
        />
      </v-card-text>

      <v-card-actions>
        <v-spacer />
        <v-btn
          @click="$emit('input', false)"
          text
        >
          {{ $t('buttons.cancel') }}
        </v-btn>
        <v-btn
          :loading="loading"
          @click="update"
        >
          {{ $t('buttons.update') }}
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>
<script>
export default {
  name: 'ChangeTextModal',
  props: {
    value: Boolean
  },
  data: () => ({
    loading: false,
    text: ''
  }),
  mounted () {
    this.text = JSON.parse(JSON.stringify(this.$store.state.welcomeMessages.find(a => a.language === this.$store.state.selectedLanguage.id && a.message_type === 2))).message
  },
  methods: {
    update () {
      this.loading = true
      this.$org.updateText(this.text, this.$store.state.selectedLanguage.id).then((data) => {
        this.$emit('input', false)
        this.$emit('emailUpdated')
        this.$store.dispatch('showSnackbar', this.$t('settings.textChanged'))
      }).catch((error) => {
        this.$store.dispatch('showSnackbar', this.$t('settings.textCouldNotBeChanged'))
      }).finally(() => {
        this.loading = false
      })
    }
  }
}
</script>
