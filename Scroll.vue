<template>
  <div>
    <div ref="container_dom" class="scroll-container" @scroll="scrollEvent" @click="clickEvent">
      <div class="scroll-phantom"></div>
      <div class="scroll-list">
        <slot :visibleData="visibleData">
        </slot>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref } from 'vue';

export default {
  props: {
    datas: {
      type: Array,
      required: true,
    },
    itemSize: {
      type: Number,
      required: true,
    },
  },
  setup(props, context) {
    let datas = props.datas;
    let itemSize = props.itemSize;
    let screenSize: number = 0;
    let visibleCount: number = 0;
    let animationId: number = 0;
    let framesPerMove: number = 60;
    let pixelsPerMove: number = itemSize;

    const startIndex = ref<number>(0);
    const endIndex = ref<number>(0);
    const offsetY = ref<number>(0);
    const container_dom = ref<HTMLElement | null>(null);

    return {
      datas,
      itemSize,
      screenSize,
      visibleCount,
      animationId,
      framesPerMove,
      pixelsPerMove,
      startIndex,
      endIndex,
      offsetY,
      container_dom,
      context
    };
  },

  methods: {
    scrollEvent(event: Event) {
      const scrollTop = (event.target as HTMLElement).scrollTop;
      this.startIndex = Math.floor(scrollTop / this.itemSize);
      this.endIndex = this.startIndex + this.visibleCount;
      this.offsetY = this.startIndex * this.itemSize;
    },
    clickEvent(event: Event) {
      this.context.emit('clickTarget', event);
    },
    autoScroll() {
      const isScrollBottom = () => {
        if (this.container_dom) {
          if (this.container_dom.scrollHeight > this.container_dom.clientHeight) {
            return Math.abs(this.container_dom.scrollHeight - this.container_dom.clientHeight - this.container_dom.scrollTop) < 1;
          } else {
            return false;
          }
        }
      };
      const scrollSpeed = () => {
        if ((this.pixelsPerMove / this.framesPerMove) < 0.8) {
          return 0.8;
        } else {
          return this.pixelsPerMove / this.framesPerMove
        }
      }
      const scroll = () => {
        if (this.container_dom) {
          this.animationId = requestAnimationFrame(scroll);
          this.container_dom.scrollBy(0, scrollSpeed());
          if (isScrollBottom()) {
            cancelAnimationFrame(this.animationId);
            this.container_dom.scrollTo(0, 0);
            setTimeout(() => {
              scroll();
            }, 500);
          }
        };
      }

      scroll();
    },
  },

  updated() {

  },

  mounted() {
    if (this.container_dom) {
      this.screenSize = this.container_dom.clientHeight;
      this.visibleCount = this.screenSize / this.itemSize;
      this.startIndex = 0;
      this.endIndex = this.startIndex + this.visibleCount;
      this.offsetY = this.startIndex * this.itemSize;
      this.autoScroll();
    }
  },

  computed: {
    listItemSize() {

    },
    visibleData() {
      return this.datas.slice(this.startIndex, this.endIndex + this.visibleCount);
    },
    transaleY() {
      return `translateY(${this.offsetY}px)`;
    },
    height() {
      return `${this.datas.length * this.itemSize}px`
    }
  },

  beforeUnmount() {
    cancelAnimationFrame(this.animationId);
  }
};
</script>

<style scoped>
.scroll-container {
  width: 100%;
  height: 100%;
  position: relative;
  overflow-y: auto;
  box-sizing: border-box;
}

.scroll-phantom {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  z-index: -1;
  height: v-bind(height);
}

.scroll-list {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  transform: v-bind(transaleY);
}
</style>
