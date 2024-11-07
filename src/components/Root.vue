<template>
  <div class="v3-input-emoji-picker">
    <div class="v3-input-picker-root">
      <div
        class="v3-input-picker-wrap"
        :class="open ? 'v3-picker-is-open' : ''"
      >
        <button
          ref="button"
          type="button"
          class="v3-input-picker-icon"
          @click="open = !open"
        >
          <img :src="face" alt="" />
        </button>

        <div
          ref="picker"
          class="v3-emoji-picker"
          :class="'v3-color-theme-' + colorTheme"
        >
          <Header />
          <Body @select="onSelect" />
          <Footer />
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
/**
 * External dependencies
 */
import {
  defineComponent,
  ref,
  onMounted,
  onBeforeUnmount,
  inject,
  computed,
} from 'vue'
import { createPopper } from '@popperjs/core'

/**
 * Internal dependencies
 */
import smileys_people from '../svgs/groups/smileys_people.svg'
import Body from './Body.vue'
import Header from './Header.vue'
import Footer from './Footer.vue'
import { EmojiExt, Store } from '../types'

export default defineComponent({
  name: 'PickerRoot',
  components: {
    Header,
    Body,
    Footer,
  },
  props: {
    additionalGroups: {
      type: Object,
      default: () => ({}),
    },
    groupOrder: {
      type: Array,
      default: () => [],
    },
    groupIcons: {
      type: Object,
      default: () => ({}),
    },
    groupNames: {
      type: Object,
      default: () => ({}),
    },
  },
  emits: {
    select: (emoji: EmojiExt) => true,
  },
  setup(props, { emit }) {
    const button = ref<HTMLButtonElement>()
    const picker = ref<any>()
    const open = ref(false)
    const { state } = inject('store') as Store
    const colorTheme = computed(() => state.options.colorTheme)

    /**
     * Functions
     */
    function onSelect(emoji: EmojiExt) {
      emit('select', emoji)
      open.value = false
    }

    function clickListener(event: MouseEvent) {
      const isOutside = !(event.target as HTMLElement)?.closest(
        '.v3-input-picker-wrap'
      )
      if (isOutside && open.value) {
        open.value = false
      }
    }

    function setupPopper() {
      if (button.value && picker.value) {
        let offset = state.options.offset

        if (typeof offset !== 'number') {
          offset = 6
        }

        createPopper(button.value, picker.value, {
          placement: 'bottom-end',
          modifiers: [
            {
              name: 'offset',
              options: {
                offset: [0, offset],
              },
            },
          ],
        })

        document.body.addEventListener('click', clickListener)
      }
    }

    /**
     * Lifecycle
     */
    onMounted(() => {
      setupPopper()
    })

    // cleanup
    onBeforeUnmount(() => {
      document.body.removeEventListener('click', clickListener)
    })

    /**
     * Return vars
     */
    return {
      face: smileys_people,
      open,
      onSelect,
      button,
      picker,
      colorTheme,
    }
  },
})
</script>
