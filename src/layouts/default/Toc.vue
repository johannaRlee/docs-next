<template>
  <v-navigation-drawer
    id="default-toc"
    v-scroll="onScroll"
    :color="dark ? '#121212' : undefined"
    :right="!rtl"
    app
    class="py-4 pr-3"
    clipped
    floating
    width="256"
  >
    <headline
      class="mb-2"
      path="contents"
    />

    <ul>
      <router-link
        v-for="({ to, level, text }, i) in toc"
        #default="{ href, navigate, isActive }"
        :key="text"
        :to="to"
      >
        <li
          :class="{
            'primary--text router-link-active': isActive,
            'text--disabled': !isActive,
            'pl-6': level === 3,
            'pl-9': level === 4
          }"
          class="pl-3 text-body-2 py-1 font-weight-regular"
        >
          <a
            :href="href"
            class="v-toc-link d-block transition-swing text-decoration-none"
            @click.prevent.stop="onClick(to, i)"
            v-text="text"
          />
        </li>
      </router-link>
    </ul>
  </v-navigation-drawer>
</template>

<script>
  // Utilities
  import { get } from 'vuex-pathify'
  import { wait } from '@/util/helpers'

  export default {
    name: 'DefaultToc',

    data: () => ({
      offsets: [],
      raf: null,
      scrolling: false,
      timeout: null,
    }),

    computed: {
      ...get('user', [
        'rtl',
        'theme@dark',
      ]),
      ...get('route', [
        'hash',
        'path',
      ]),
      initializing: get('app/initializing'),
      toc: get('pages/toc'),
    },

    methods: {
      async onClick (hash, i) {
        if (this.hash === hash) return

        this.scrolling = true

        this.$router.replace({ path: this.path, hash })

        await this.$vuetify.goTo(hash)
        await wait(200)

        this.scrolling = false
      },
      setOffsets () {
        const offsets = []
        const toc = this.toc.slice().reverse()

        for (const item of toc) {
          const section = document.getElementById(item.to.slice(1))

          if (!section) continue

          offsets.push(section.offsetTop - 48)
        }

        this.offsets = offsets
      },
      findActiveIndex () {
        const currentOffset = (
          window.pageYOffset ||
          document.documentElement.offsetTop ||
          0
        )

        // If we are at the top of the page
        // reset the offset
        if (currentOffset === 0) {
          if (this.hash) {
            this.$router.replace({ path: this.path })
          }

          return
        }

        if (
          this.offsets.length !== this.toc.length
        ) this.setOffsets()

        const index = this.offsets.findIndex(offset => {
          return offset < currentOffset
        })

        const tindex = index > -1
          ? this.offsets.length - 1 - index
          : 0

        const hash = this.toc[tindex].to

        if (hash === this.hash) return

        this.$router.replace({
          path: this.path,
          hash,
        })
      },
      onScroll () {
        cancelAnimationFrame(this.raf)
        clearTimeout(this.timeout)

        if (this.scrolling || this.initializing) return

        this.timeout = setTimeout(this.findActiveIndex, 17)
      },
    },
  }
</script>

<style lang="sass">
  #default-toc
    ul
      list-style-type: none

    li
      border-left: 2px solid #E5E5E5

      &.router-link-active
        border-left-color: currentColor

    .v-toc-link
      color: inherit

    &.theme--dark
      li:not(.router-link-active)
        border-left-color: rgba(255, 255, 255, 0.5)
</style>
