<template>
  <view @touchstart="handleTouchstart" @touchend="handleTouchend">触屏</view>
</template>

<script>
export default {
  data() {
    return {
      // 按下时间
      startTime: 0,
      // 按下坐标
      startX: 0,
      startY: 0,
    };
  },
  methods: {
    handleTouchstart(event) {
      //   console.log("按下");
      // console.log("按下" + event.changedTouches[0].clientX);
      // console.log("按下" + event.changedTouches[0].clientY);

      this.startTime = Date.now();
      this.startX = event.changedTouches[0].clientX;
      this.startY = event.changedTouches[0].clientY;
    },
    handleTouchend(event) {
      //   console.log("离开");
      // console.log("离开" + event.changedTouches[0].clientX);
      // console.log("离开" + event.changedTouches[0].clientY);

      const endTime = Date.now();
      const endX = event.changedTouches[0].clientX;
      const endY = event.changedTouches[0].clientY;

      // 判断按下的时长
      if (endTime - this.startTime > 2000) {
        return;
      }

      // 滑动的方向
      let direction = "";

      // 判断用户滑动的距离
      if (Math.abs(endX - this.startX) > 10) {
        // 滑动方向
        direction = endX - this.startX > 0 ? "right" : "left";
      } else {
        return;
      }

      console.log(direction);
    },
  },
};
</script>

<style>
view {
  width: 100%;
  height: 500rpx;
  background-color: aqua;
}
</style>