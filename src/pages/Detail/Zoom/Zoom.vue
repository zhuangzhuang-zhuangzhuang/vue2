<template>
  <div class="spec-preview">
    <img :src="defaultImage.imgUrl" />
    <div class="event" @mousemove="move"></div>
    <div class="big">
      <img :src="defaultImage.imgUrl" ref="bigImg" />
    </div>
    <div class="mask" ref="mask"></div>
  </div>
</template>

<script>
export default {
  name: "Zoom",
  props: ["imageList"],
  data() {
    return {
      defalutIndex: 0,
    };
  },
  mounted() {
    this.$bus.$on("changedefaultIndex", this.changedefaultIndex);
  },
  computed: {
    defaultImage() {
      return this.imageList[this.defalutIndex] || {};
    },
  },
  methods: {
    changedefaultIndex(index) {
      this.defalutIndex = index;
    },
    move(event) {
      //鼠标动，蒙版动  想办法去让蒙版跟着动
      // 转化为根据鼠标的位置求蒙版的位置
      // event.clientX    相对视口左上角，视口是不变的
      // event.pageX      相对页面左上角
      // event.offsetX    相对元素本身左上角
      let mask = this.$refs.mask;
      let bigImg = this.$refs.bigImg;
      //获取鼠标位置
      let mouseX = event.offsetX;
      let mouseY = event.offsetY;
      //根据鼠标位置和蒙版的宽度计算蒙版的位置
      let maskX = mouseX - mask.offsetWidth / 2;
      let maskY = mouseY - mask.offsetHeight / 2;
      //设置蒙版位置之前，把边界给限定一下
      if (maskX < 0) {
        maskX = 0;
      } else if (maskX > mask.offsetWidth) {
        maskX = mask.offsetWidth;
      }
      if (maskY < 0) {
        maskY = 0;
      } else if (maskY > mask.offsetHeight) {
        maskY = mask.offsetHeight;
      }
      //设置蒙版的位置让蒙版动
      mask.style.left = maskX + "px";
      mask.style.top = maskY + "px";
      //蒙版动  大图动   大图刚好移动蒙版反向2倍
      bigImg.style.left = -2 * maskX + "px";
      bigImg.style.top = -2 * maskY + "px";
    },
  },
};
</script>

<style lang="less">
.spec-preview {
  position: relative;
  width: 400px;
  height: 400px;
  border: 1px solid #ccc;

  img {
    width: 100%;
    height: 100%;
  }

  .event {
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 998;
  }

  .mask {
    width: 50%;
    height: 50%;
    background-color: rgba(0, 255, 0, 0.3);
    position: absolute;
    left: 0;
    top: 0;
    display: none;
  }

  .big {
    width: 100%;
    height: 100%;
    position: absolute;
    top: -1px;
    left: 100%;
    border: 1px solid #aaa;
    overflow: hidden;
    z-index: 998;
    display: none;
    background: white;

    img {
      width: 200%;
      max-width: 200%;
      height: 200%;
      position: absolute;
      left: 0;
      top: 0;
    }
  }

  .event:hover ~ .mask,
  .event:hover ~ .big {
    display: block;
  }
}
</style>