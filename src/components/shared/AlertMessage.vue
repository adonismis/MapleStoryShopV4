<template>
  <div class="message-alert">
    <div
      class="alert alert-dismissible"
      :class="'alert-' + item.status"
      v-for="(item, i) in messages"
      :key="i"
    >
      {{ item.message }}
      <button
        type="button"
        class="close"
        @click="removeMessage(i)"
        aria-label="Close"
      >
        <span aria-hidden="true">&times;</span>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Navbar',
  methods: {
    updateMessage(message, status) {
      this.$store.dispatch('updateMessage', { message, status });
    },
    removeMessage(num) {
      this.$store.dispatch('removeMessage', num);
    },
    removeMessageWithTiming(timestamp) {
      const vm = this;
      setTimeout(() => {
        vm.messages.forEach((item, i) => {
          if (item.timestamp === timestamp) {
            vm.messages.splice(i, 1);
          }
        });
      }, 5000);
    },
  },
  computed: {
    messages() {
      return this.$store.state.messages;
    },
  },
};
</script>

<style scope lang="scss">
.message-alert {
  position: fixed;
  width: 250px;
  top: 69px;
  left: 50%;
  margin-left: -125px;
  z-index: 1100;
}
</style>
