<template>
  <div class="swiper-container" ref="detailSwiper">
    <div class="swiper-wrapper">
      <div class="swiper-slide" v-for="(img, index) in imageList" :key="img.id">
        <img
          :src="img.imgUrl"
          :class="{ active: index === defaultIndex }"
          @click="changedefaultIndex(index)"
        />
      </div>
    </div>
    <div class="swiper-button-next"></div>
    <div class="swiper-button-prev"></div>
  </div>
</template>

<script>
import Swiper from "swiper";
export default {
  name: "ImageList",
  props: ["imageList"],
  data() {
    return {
      defaultIndex: 0,
    };
  },
  methods: {
    changedefaultIndex(index) {
      this.defaultIndex = index;
      this.$bus.$emit("changedefaultIndex", index);
    },
  },
  watch: {
    imageList: {
      immediate: true, //这里加它没什么用，就是为了让和floor的代码一致
      handler(newval, oldval) {
        this.$nextTick(() => {
          new Swiper(this.$refs.detailSwiper, {
            slidesPerView: 5,
            slidesPerGroup: 5,
            // 如果需要前进后退按钮
            navigation: {
              nextEl: ".swiper-button-next",
              prevEl: ".swiper-button-prev",
            },
          });
        });
      },
    },
  },
};
</script>

<style lang="less" scoped>
.swiper-container {
  height: 56px;
  width: 412px;
  box-sizing: border-box;
  padding: 0 12px;

  .swiper-slide {
    width: 56px;
    height: 56px;

    img {
      width: 100%;
      height: 100%;
      border: 1px solid #ccc;
      padding: 2px;
      width: 50px;
      height: 50px;
      display: block;

      &.active {
        border: 2px solid #f60;
        padding: 1px;
      }

      &:active {
        border: 2px solid #f60;
        padding: 1px;
      }
    }
  }

  .swiper-button-next {
    left: auto;
    right: 0;
  }

  .swiper-button-prev {
    left: 0;
    right: auto;
  }

  .swiper-button-next,
  .swiper-button-prev {
    box-sizing: border-box;
    width: 12px;
    height: 56px;
    background: rgb(235, 235, 235);
    border: 1px solid rgb(204, 204, 204);
    top: 0;
    margin-top: 0;
    &::after {
      font-size: 12px;
    }
  }
}
</style>