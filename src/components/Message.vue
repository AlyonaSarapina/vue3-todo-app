<template>
  <article
    class="message"
    :class="{ 'message--hidden': !active }"
  >
    <div class="message-header">
      <slot name="header"></slot>
      <button class="delete" @click="hide"></button>
    </div>
    <div class="message-body">
      <slot :x="404">Message</slot>
    </div>
  </article>
</template>

<script>
export default {
  props: {
    active: Boolean,
  },
  emits: ['hide'],
  methods: {
    hide() {
      this.$emit('hide');
    }
  }
}
</script>