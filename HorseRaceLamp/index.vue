<template>
  <div class="horse-race-lamp">
    <div class="pre" @click="prev">&lt;</div>
    <div class="slick-list">
      <div
        class="slick-track"
        :style="{ transform: `translate3d(${translatex}px,0,0)` }"
      >
        <div
          class="gallery-indicator-image slick-slide"
          v-for="(item, index) in columns"
          :key="index"
          :class="{ selected: selected === index }"
          @click="selectItem(index)"
        >
          <img :src="item.imageUrl" :alt="item.alt" :title="item.title" />
        </div>
      </div>
    </div>
    <div class="next" @click="next">&gt;</div>
  </div>
</template>

<script>
  export default {
    props: {
      columns: {
        type: Array,
        default: []
      }
    },
    data() {
      return {
        translatex: 0,
        selected: 0
      }
    },
    methods: {
      prev() {
        if (this.selected === 0) {
          this.selected = this.columns.length - 1
        } else {
          this.selected--
        }
        this.updateTranslatex()
      },
      next() {
        if (this.selected + 1 === this.columns.length) {
          this.selected = 0
          this.translatex = 0
        } else {
          this.selected++
        }
        this.updateTranslatex()
      },
      selectItem(index) {
        this.selected = index
        this.updateTranslatex()
      },
      updateTranslatex() {
        const itemWidth = 128 // 假设每个项目的宽度为 128px
        if (this.selected >= 2 && this.selected <= this.columns.length - 3) {
          this.translatex = [(this.selected - 3) * -itemWidth] - itemWidth
        }
        if (this.selected == 0) {
          this.translatex = 0
        }
        if (this.selected == this.columns.length - 1) {
          this.translatex = [(this.selected - 5) * -itemWidth] - itemWidth
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
  .horse-race-lamp {
    display: flex;

    .next,
    .pre {
      line-height: 82px;
      width: 30px;
      text-align: center;
      font-size: 20px;
      color: #cac9c9;
      opacity: 0.8;
      filter: alpha(opacity = 80);
      background-color: #000;
      -webkit-user-select: none;
      -moz-user-select: none;
      -ms-user-select: none;
      user-select: none;
      cursor: pointer;
    }
    .slick-list {
      white-space: nowrap;
      overflow: hidden;
      height: 82px;
      font-size: 0;
      padding: 0;
      margin: 0;
      width: 638px;
      .slick-track {
        transition: transform 500ms ease;
      }
      .gallery-indicator-image {
        display: inline-block;
        position: relative;
        width: 112px;
        height: 70px;
        outline: ivory;
        margin: 8px;
        img {
          width: 100%;
          height: 100%;
          opacity: 0.5;
        }
      }
      .selected img {
        opacity: 1;
      }
    }
  }
</style>
