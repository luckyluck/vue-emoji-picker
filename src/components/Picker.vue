<template>
  <picker-root @select="$emit('select', $event)" />
</template>

<script lang="ts">
/**
 * External dependencies
 */
import { defineComponent, provide, PropType } from 'vue'

/**
 * Internal dependencies
 */
import { COLOR_THEMES, GROUP_NAMES, STATIC_TEXTS } from '../constant'
import Store from '../store'
import PickerRoot from './Root.vue'
import { ColorTheme, EmojiExt } from '../types'

export default defineComponent({
  name: 'Picker',
  components: {
    PickerRoot,
  },
  props: {
    native: {
      type: Boolean,
      default: false,
    },
    hideSearch: {
      type: Boolean,
      default: false,
    },
    hideGroupIcons: {
      type: Boolean,
      default: false,
    },
    hideGroupNames: {
      type: Boolean,
      default: false,
    },
    staticTexts: {
      type: Object,
      default: () => ({}),
    },
    disableStickyGroupNames: {
      type: Boolean,
      default: false,
    },
    disabledGroups: {
      type: Array,
      default: () => [],
    },
    groupNames: {
      type: Object,
      default: () => ({}),
    },
    disableSkinTones: {
      type: Boolean,
      default: false,
    },
    text: {
      type: String,
      default: '',
    },
    mode: {
      type: String,
      default: 'insert', // or append, prepend
    },
    offset: {
      type: Number,
      default: 6,
    },
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
    displayRecent: {
      type: Boolean,
      default: false,
    },
    theme: {
      type: String as PropType<ColorTheme>,
      default: 'light',
    },
  },
  emits: {
    select: (emoji: EmojiExt) => true,
  },
  setup(props, { emit }) {
    /**
     * Create a brand new store and
     */
    const store = Store()

    /**
     * Initializing initial props
     */
    store.updateOptions({
      native: props.native,
      hideSearch: props.hideSearch,
      hideGroupIcons: props.hideGroupIcons,
      hideGroupNames: props.hideGroupNames,
      staticTexts: { ...STATIC_TEXTS, ...props.staticTexts },
      disableStickyGroupNames: props.disableStickyGroupNames,
      disabledGroups: props.disabledGroups,
      groupNames: { ...GROUP_NAMES, ...props.groupNames },
      disableSkinTones: props.disableSkinTones,
      displayRecent: props.displayRecent,
      additionalGroups: props.additionalGroups,
      mode: props.mode,
      offset: props.offset,
      groupOrder: props.groupOrder,
      groupIcons: props.groupIcons,
      colorTheme: COLOR_THEMES.includes(props.theme) ? props.theme : 'light',
    })

    /**
     * (provide) make available for entire app.
     */
    provide('store', store)

    /**
     * Return vars
     */

    return {}
  },
})
</script>
