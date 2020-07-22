<template>
  <ul class="scroller-container">
    <li
      class="scroller-item"
      v-for="(item, index) in sliceList"
      :id="index === 0 ? 'start' : (index === sliceList.length - 1 ? 'end' : '')"
      :key="index"
      :style="{top: (index + start) * (200 + 10) + 'px'}"
    >{{ item }}</li>
  </ul>
</template>

<script>
export default {
  props: {
    list: {
      type: Array,
      default: () => []
    },
    threshold: {
      type: Number,
      default: 20
    }
  },
  data() {
    return {
      observer: null,
      start: 0,
      end: this.threshold,
      offset: Math.min(this.threshold, 10)
    };
  },
  computed: {
    sliceList() {
      return this.list.slice(this.start, this.end);
    }
  },
  methods: {
    initObserver() {
      this.observer = new IntersectionObserver(
        entires => {
          entires.forEach((entry, index) => {
            if (
              entry.isIntersecting &&
              entry.target.id === "start" &&
              this.start > 0
            ) {
              this.start = Math.max(this.start - this.offset, 0);
              this.end = this.start + this.threshold;
              console.log("scroll up", this.start, this.end, this.sliceList);
            }
            if (
              entry.isIntersecting &&
              entry.target.id === "end" &&
              this.end < this.list.length
            ) {
              this.end = Math.min(this.end + this.offset, this.list.length);
              this.start = this.end - this.threshold;
              console.log("scroll down", this.start, this.end, this.sliceList);
            }
          });
        },
        {
          root: document.querySelector(".scroller-container")
        }
      );
      this.observer.observe(document.querySelector("#start"));
      this.observer.observe(document.querySelector("#end"));
    },
    uninstallObserver() {
      if (this.observer) {
        this.observer.unobserve(document.querySelector("#start"));
        this.observer.unobserve(document.querySelector("#end"));
      }
    }
  },
  mounted() {
    this.initObserver();
  },
  beforeDestroy() {
    this.uninstallObserver();
  }
};
</script>

<style lang="less" scoped>
.scroller-container {
  position: relative;
  height: 500px;
  width: 100%;
  padding: 0;
  overflow: scroll;
}
.scroller-item {
  position: absolute;
  height: 200px;
  width: 100%;
  border-radius: 10px;
  background-color: #cccccc;
  list-style-type: none;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #ffffff;
  font-size: 30px;
}
</style>

