<template>
  <scroll-view
    @scrolltolower="handleToLower"
    class="recommend_view"
    scroll-y=""
    v-if="recommends.length > 0"
  >
    <!-- 推荐 开始 -->
    <view class="recommonds_wrap">
      <navigator
        class="recommonds_item"
        v-for="item in recommends"
        :key="item.id"
        :url="`/pages/album/index?id=${item.target}`"
      >
        <image mode="widthFix" :src="item.thumb" alt=""></image>
      </navigator>
    </view>
    <!-- 推荐 结束 -->

    <!-- 月份 -->
    <view class="monthes_wrap">
      <view class="monthes_tilte">
        <view class="monthes_tilte_info">
          <view class="monthes_info">
            <text>{{ monthes.DD }} / </text>
            {{ monthes.MM }} 月
          </view>
          <view class="monthes_text">你负责美丽就好</view>
        </view>
        <view class="monthes_tilte_more">更多 > </view>
      </view>
      <view class="monthes_content">
        <view
          class="monthes_item"
          v-for="(item, index) in monthes.items"
          ::key="item.id"
        >
          <go-detail :list="monthes.items" :index="index">
            <image
              mode="aspectFill"
              :src="item.thumb + item.rule.replace('$<Height>', 360)"
            ></image>
          </go-detail>
        </view>
      </view>
    </view>

    <!-- 热门 -->
    <view class="hots_wrap">
      <view class="hosts_title">
        <text>热门</text>
      </view>
      <view class="hots_content">
        <view class="host_item" v-for="(item, index) in hots" :key="item.id">
          <go-detail :list="hots" :index="index">
            <image mode="widthFix" :src="item.thumb"></image>
          </go-detail>
        </view>
      </view>
    </view>
  </scroll-view>
</template>

<script>
import moment from "moment";
import goDetail from "@/components/goDetail";
export default {
  components: {
    goDetail,
  },
  data() {
    return {
      // 推荐列表
      recommends: [],
      // 月份模块
      monthes: {},
      // 热门
      hots: [],
      // 请求的参数
      params: {
        // 要获取几条
        limit: 30,
        // 关键字
        order: "hot",
        // 要跳过几条
        skip: 0,
      },
      // 是否还有下一页
      hasMore: true,
    };
  },
  mounted() {
    this.getList();
  },
  methods: {
    getImages(_url) {
      if (_url !== undefined) {
        let _u = _url.substring(7);
        return "https://images.weserv.nl/?url=" + _u;
      }
    },
    // 获取接口的数据
    getList() {
      this.request({
        // url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
        url: "http://service.picasso.adesk.com/v3/homepage/vertical",
        data: this.params,
      }).then((res) => {
        console.log(res);
        if (res.res.vertical.length === 0) {
          this.hasMore = false;
          return;
        }
        if (this.recommends.length === 0) {
          // 第一次
          // 推荐模块
          this.recommends = res.res.homepage[1].items;

          // 月份模块
          this.monthes = res.res.homepage[2];
          // 将时间戳 改成 18号/月 moment.js
          this.monthes.MM = moment(this.monthes.stime).format("MM");
          this.monthes.DD = moment(this.monthes.stime).format("DD");
        }
        // 获取热门数据列表
        this.hots = [...this.hots, ...res.res.vertical];
      });
    },
    // 滚动条触底时间
    handleToLower() {
      /* 
        1.修改参数 skip+=limit
        2.重新发送请求 getList()
        3.请求成功 hots 数据的叠加
      */
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "没有数据了",
          icon: "none",
        });
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.recommend_view {
  // 屏幕高度 - 头部标题的高度
  height: calc(100vh - 36px);
}
.recommonds_wrap {
  // flex布局
  display: flex;
  flex-wrap: wrap;
  .recommonds_item {
    width: 50%;
    border: 5rpx solid #fff;
  }
}

.monthes_wrap {
  .monthes_tilte {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .monthes_tilte_info {
      color: $color;
      font-size: 30rpx;
      font-weight: 600;
      display: flex;
      .monthes_info {
        text {
          font-size: 36rpx;
        }
      }

      .monthes_text {
        font-size: 34rpx;
        color: #666;
        margin-left: 30rpx;
      }

      .monthes_tilte_more {
        font-size: 24rpx;
        color: $color;
      }
    }
  }

  .monthes_content {
    display: flex;
    flex-wrap: wrap;
    .monthes_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}
.hots_wrap {
  .hosts_title {
    padding: 20rpx;
    text {
      border-left: 5rpx solid $color;
      padding-left: 20rpx;
      font-size: 34rpx;
      font-weight: 600;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;
    .host_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      image {
      }
    }
  }
}
</style>