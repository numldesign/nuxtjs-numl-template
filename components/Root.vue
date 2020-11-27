<template>
  <nu-root padding="3x" font="Quicksand">
    <slot></slot>
  </nu-root>
</template>

<script>
import Vue from 'vue'

Vue.config.ignoredElements = [/^nu-/]

function requireNude() {
  if (typeof window === 'undefined') return new Promise(() => {})

  if (window.Nude) {
    return Promise.resolve(window.Nude)
  } else {
    return new Promise((resolve) => {
      window.addEventListener('nudeReady', () => {
        resolve(window.Nude)
      })
    })
  }
}

function initNude(router) {
  const { Nude } = window

  Nude.routing.setRouter((url, openNewTab) => {
    // skip outside links and links that open in new tabs
    if (
      openNewTab ||
      url.startsWith('https://') ||
      url.includes('//') ||
      url.startsWith('mailto:') ||
      url.includes('/api/')
    ) {
      return true
    }

    router.push(url) // handle routing by Vue Router

    return false
  })

  // OPTIONAL: define custom units
  Nude.units.define('gp', 'var(--nu-grid-gap)')

  // OPTIONAL: define new elements
  Nude.define('nu-meme', {
    styles: {
      display: 'block',
      padding: '1x',
      bg: '#mark',
    },
  })

  // OPTIONAL: assign new options to the existing elements
  Nude.define('nu-card', {
    styles: {
      padding: '.5x 1x',
    },
  })

  // manually trigger Nude initialization
  Nude.init()
}

export default {
  name: 'Root',
  async mounted() {
    await requireNude()

    initNude(this.$router)
  },
}
</script>
