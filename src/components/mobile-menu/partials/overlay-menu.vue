<template>
  <transition
      :name="transitionName"
      @after-enter="afterTransitionEnter"
      @before-leave="beforeTransitionLeave"
  >
    <div
        v-show="show"
        :class="menuClasses"
        :style="transitionStyle"
    >
      <div class="overlay-menu-inner">
        <div class="overlay-menu-ripple" :style="rippleStyle"></div>
        <div ref="content" class="overlay-menu-content">
          <slot></slot>
        </div>
      </div>
    </div>
  </transition>
</template>
<script>
export default {
  name: 'overlay-menu',

  props: {
    // Required to get the click coordinates.
    toggleEvent: {
      required: true,
    },
    transitionName: {
      type: String,
      default: 'v-ripple-in',
    },
  },

  data: () => ({
    show: false,
    hasTransitioned: false,
    rippleOriginX: 0,
    rippleOriginY: 0,
    rippleSize: 0,
    lastScroll: 0,
    initialScrollBehavior: getComputedStyle(document.documentElement).getPropertyValue('scroll-behavior'),
  }),

  computed: {
    allowShow() {
      return (
          this.show && !document.body.classList.contains('active-overlay')
      );
    },
    transitionStyle() {
      return {
        'transform-origin': `${this.rippleOriginX}% ${this.rippleOriginY}%`,
      };
    },
    menuClasses() {
      return [
        'overlay-menu',
        {'overlay-menu-active': this.hasTransitioned},
      ];
    },
    rippleStyle() {
      return {
        height: `${this.rippleSize}px`,
        width: `${this.rippleSize}px`,
      };
    },
  },

  watch: {
    toggleEvent($event) {
      this.show = !this.show;
      this.setRipple($event);
    },
  },

  beforeUnmount() {
    document.body.classList.remove('active-overlay');
  },

  methods: {
    setRipple({clientX, clientY}) {
      const {max, ceil, floor} = Math;

      const vw = document.documentElement.clientWidth;
      const vh = window.innerHeight;

      this.rippleSize = max(ceil(vw), ceil(vh)) * 1.5;
      this.rippleOriginX = floor(this.rangePercentage(clientX, 0, vw));
      this.rippleOriginY = floor(this.rangePercentage(clientY, 0, vh));
    },

    rangePercentage(x, min, max) {
      return ((x - min) * 100) / (max - min);
    },

    afterTransitionEnter() {
      if (!this.allowShow) return;

      this.lastScroll = window.pageYOffset;
      document.body.classList.add('active-overlay');

      if (this.initialScrollBehavior !== 'auto') {
        document.documentElement.style.setProperty(
            'scroll-behavior',
            'auto'
        );
      }

      this.hasTransitioned = true;
    },

    beforeTransitionLeave() {
      document.body.classList.remove('active-overlay');
      window.scrollTo(0, this.lastScroll);

      if (this.initialScrollBehavior !== 'auto') {
        document.documentElement.style.setProperty(
            'scroll-behavior',
            this.initialScrollBehavior
        );
      }

      this.hasTransitioned = false;
    },
  }
}
</script>