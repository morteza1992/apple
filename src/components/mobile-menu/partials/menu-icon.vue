<template>
  <button
      :class="['menu-icon', { 'menu-icon-active': active }]"
      @click.prevent="toggle"
      type="button"
      name="menu-icon"
      aria-label="Toggle menu"
  >
    <span></span>
  </button>
</template>
<script>
export default {
  name: 'menu-icon',
  data: () => ({
    active: false,
  }),
  methods: {
    toggle($event) {
      this.active = !this.active;
      this.$emit('click', $event);
    },
  },
}
</script>