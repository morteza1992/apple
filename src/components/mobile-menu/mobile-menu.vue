<template>
  <div>

        <menuIcon
            @click="menuTriggerEvent = $event"
        ></menuIcon>

        <overlayMenu :toggle-event="menuTriggerEvent" v-cloak>
          <ul class="list">
            <li>Menu item 1</li>
            <li>Menu item 2</li>
          </ul>
        </overlayMenu>

  </div>
</template>
<script>
import './style/menu.scss'
import menuIcon from './partials/menu-icon.vue'
import overlayMenu from './partials/overlay-menu.vue'

export default {
  components: {
    menuIcon,
    overlayMenu
  },
  data() {
    return {
      menuTriggerEvent: null, // event
    }
  }

}
</script>
