<template>
  <v-container
    class="pa-0"
    fluid
  >
    <v-row
      align="center"
      class="fill-height"
      justify="center"
    >
      <v-col
        v-for="(layout, i) in layouts"
        :key="i"
        cols="12"
        sm="6"
        lg="4"
      >
        <v-card>
          <v-img
            :alt="layout.name"
            :aspect-ratio="16/9"
            :src="genSrc(layout.name)"
            width="100%"
          >
            <v-fade-transition>
              <v-overlay
                absolute
                class="text-center"
                opacity="0.7"
              >
                <h2
                  class="title mb-6"
                  v-text="layout.name"
                />
                <v-btn
                  :aria-label="$i18n.t('layout-link', { name: layout.name })"
                  :href="`/${locale}/examples/pre-made-layouts/${kebabCase(layout.name)}`"
                  :title="$i18n.t('layout-link', { name: layout.name })"
                  class="mx-2"
                  color="indigo"
                  depressed
                  fab
                  small
                  target="_blank"
                >
                  <v-icon>$mdiOpenInNew</v-icon>
                </v-btn>
                <v-btn
                  :aria-label="$i18n.t('layout-link', { name: layout.name })"
                  :href="`https://github.com/vuetifyjs/vuetify/tree/${branch}/${filePath}/${layout.filename}.vue`"
                  :title="$i18n.t('layout-link', { name: layout.name })"
                  class="mx-2"
                  color="indigo"
                  depressed
                  fab
                  small
                  target="_blank"
                >
                  <v-icon>$mdiCodeTags</v-icon>
                </v-btn>
              </v-overlay>
            </v-fade-transition>
          </v-img>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  // Utilities
  import kebabCase from 'lodash/kebabCase'
  import { get } from 'vuex-pathify'

  export default {
    name: 'LayoutExamples',

    data: () => ({
      filePath: 'packages/docs/src/layouts/layouts/demos',
      layouts: [
        {
          name: 'Baseline',
          filename: 'baseline',
        },
        {
          name: 'Baseline Flipped',
          filename: 'baseline-flipped',
        },
        {
          name: 'Centered',
          filename: 'centered',
        },
        {
          name: 'Complex',
          filename: 'complex',
        },
        {
          name: 'Dark',
          filename: 'dark',
        },
        {
          name: 'Google Contacts',
          filename: 'google-contacts',
        },
        {
          name: 'Google Keep',
          filename: 'google-keep',
        },
        {
          name: 'Google Youtube',
          filename: 'google-youtube',
        },
        {
          name: 'Sandbox',
          filename: 'sandbox',
        },
      ],
    }),

    computed: {
      branch: get('app/branch'),
      locale: get('route/params@locale'),
    },

    methods: {
      kebabCase,
      genSrc (name) {
        return `https://cdn.vuetifyjs.com/images/layouts/${name.toLowerCase().replace(' ', '-')}.png`
      },
    },
  }
</script>
