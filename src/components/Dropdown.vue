<template>
<div class="dropdown" :class="{'is-active': opened, 'fully-visible': fullyVisible && !fullyHidden}">
  <slot name="dropdown"></slot>
  <div class="dropdown-menu" role="menu">
    <slot name="menu">
      <transition name="slide" @after-enter="onAfterEnter" @enter-cancelled="onEnterCancelled" @before-leave="onBeforeLeave" @before-enter="onBeforeEnter" @after-leave="onAfterLeave">
        <div class="dropdown-content" ref="content" v-if="opened">
          <slot name="content"></slot>
        </div>
      </transition>
    </slot>
  </div>
</div>
</template>

<script>
import { defineComponent } from 'vue-demi'

export default defineComponent({
  props: {
    show: {
      type: Boolean,
      default: false
    },
    transitionDuration: {
      type: Number,
      default: 300
    },
    maxHeight: {
      type: String,
      default: '100vh'
    },
    onOpen: {
      type: Function,
      default () {
        return () => {}
      }
    },
    onClose: {
      type: Function,
      default () {
        return () => {}
      }
    }
  },
  emits: ['update:show'],
  data () {
    return {
      opened: false,
      fullyVisible: false,
      fullyHidden: true
    }
  },
  watch: {
    show (v) {
      if (v) {
        this.open()
      } else {
        this.close()
      }
    }
  },
  computed: {
    enterDuration () {
      return `${this.transitionDuration}ms`
    },
    leaveDuration () {
      const leaveDurationMS = Math.max(0, 0.9 * this.transitionDuration)
      return `${leaveDurationMS}ms`
    }
  },
  methods: {
    async open () {
      if (this.opened) {
        return
      }
      await this.onOpen()
      this.$emit('update:show', true)
      this.opened = true
    },
    async close () {
      if (!this.opened) {
        return
      }
      await this.onClose()
      this.$emit('update:show', false)
      this.opened = false
    },
    onAfterEnter () {
      this.fullyVisible = true
      this.fullyHidden = false
    },
    onEnterCancelled () {
      this.fullyVisible = false
      this.fullyHidden = false
    },
    onBeforeLeave () {
      this.fullyVisible = false
    },
    onBeforeEnter () {
      this.fullyHidden = false
    },
    onAfterLeave () {
      this.fullyVisible = false
      this.fullyHidden = true
    }
  },
  mounted () {
    if (this.show) {
      this.open()
    }
  }
})
</script>

<style lang="scss" scoped>
.dropdown {
  --enter-duration: v-bind(enterDuration);
  --leave-duration: v-bind(leaveDuration);
  --max-height: v-bind(maxHeight);

  .dropdown-menu {
    display: block;
  }

  &.is-active.fully-visible {
    .dropdown-content {
      max-height: var(--max-height);
      overflow-y: auto;
    }
  }

  .slide-enter-active {
    transition: max-height var(--enter-duration) ease-in;
  }

  .slide-leave-active {
    transition: max-height var(--leave-duration) ease-out;
  }

  .slide-enter-to, .slide-leave-from {
    max-height: var(--max-height);
    overflow: hidden;
  }

  .slide-enter-from, .slide-leave-to {
    max-height: 0;
    overflow: hidden;
  }
}

</style>
