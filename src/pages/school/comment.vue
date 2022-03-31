// 校内商户详情
<template>
  <view class="comment">
    <uni-swiper-dot :info="business.signboard" :current="current">
      <swiper class="swiper-box" @change="change">
        <swiper-item v-for="item in business.signboard" :key="item">
          <view class="swiper-item">
            <image mode="heightFix" :src="item" @click="previewImage(item, business.signboard)"></image>
          </view>
        </swiper-item>
      </swiper>
    </uni-swiper-dot>
    <view class="main">
      <uni-title type="h1" :title="business.name"></uni-title>
      <view class="address">
        <uni-icons type="location" size="18"></uni-icons>
        <text>{{ business.address }}</text>
      </view>
      <uni-rate size="18" readonly="true" :value="overall" />
      <view class="rating" v-if="!isNaN(overall)">
        <text v-if="business.type === '餐饮'">口味:{{ taste || 0 }}</text>
        <text>环境:{{ environment || 0 }}</text>
        <text>服务:{{ service || 0 }}</text>
      </view>
      <view class="title">
        <text class="new">{{ evaluate.length ? '最新评价' : '暂无评价' }}</text>
        <button class="add" @click="add(business._id, business.signboard[0], business.name, business.type)">评价</button>
      </view>
      <view class="content">
        <ul class="list">
          <li class="item" v-for="item in evaluate" :key="item._id">
            <view class="head">
              <image class="avatar" :src="item.avatar"></image>
              <view class="text">
                <view class="top">
                  <text class="name">{{ item.name }}</text>
                  <text class="date">{{ item.date | getDate }}</text>
                </view>
                <view class="rate">
                  <view class="overall">
                    总体
                    <text class="score">{{ score[item.overall] }}</text>
                  </view>
                  <view class="taste" v-if="business.type === '餐饮'">
                    口味
                    <text class="score">{{ score[item.taste] }}</text>
                  </view>
                  <view class="environment">
                    环境
                    <text class="score">{{ score[item.environment] }}</text>
                  </view>
                  <view class="service">
                    服务
                    <text class="score">{{ score[item.service] }}</text>
                  </view>
                </view>
              </view>
            </view>
            <view class="body">
              <view class="comment" v-if="item.comment">{{ item.comment }}</view>
              <view class="images" v-if="item.images.length">
                <image class="image" mode="aspectFill" :src="item1" v-for="item1 in item.images" :key="item1" @click="previewImage(item1, item.images)"></image>
              </view>
            </view>
          </li>
        </ul>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      business: {},
      evaluate: [],
      current: 0,
      score: ['', '很差', '较差', '一般', '满意', '超赞']
    }
  },
  methods: {
    // 获取商户信息
    getBusiness(id) {
      wx.showLoading({
        title: '加载中',
        mask: true
      })
      wx.cloud
        .callFunction({
          name: 'getBusiness',
          data: {
            id
          }
        })
        .then(res => {
          this.business = res.result.data
        })
    },
    // 获取商户评论
    retrieveComment(id) {
      wx.cloud
        .callFunction({
          name: 'retrieveComment',
          data: {
            id
          }
        })
        .then(res => {
          this.evaluate = res.result.data
          wx.hideLoading()
        })
    },
    // 滑动轮播图
    change(e) {
      this.current = e.detail.current
    },
    // 预览图片
    previewImage(current, urls) {
      wx.previewImage({
        current,
        urls
      })
    },
    // 添加评论
    add(id, signboard, name, type) {
      if (!wx.getStorageSync('userInfo')) {
        wx.showModal({
          title: '提示',
          content: '请先登录',
          success(res) {
            if (res.confirm) {
              wx.switchTab({
                url: '/pages/mine/mine'
              })
            }
          }
        })
      } else {
        wx.navigateTo({
          url: `/pages/school/evaluate?id=${id}&signboard=${signboard}&name=${name}&type=${type}`
        })
      }
    }
  },
  onLoad(options) {
    this.getBusiness(options.id)
    this.retrieveComment(options.id)
  },
  filters: {
    // 日期格式化
    getDate(value) {
      const date = new Date(value)
      const yyyy = date.getFullYear()
      let MM = date.getMonth() + 1
      let dd = date.getDate()
      MM = MM < 10 ? '0' + MM : MM
      dd = dd < 10 ? '0' + dd : dd
      return yyyy + '-' + MM + '-' + dd
    }
  },
  computed: {
    // 总体评分
    overall() {
      const overallArr = Array.from(this.evaluate).map(item => item.overall)
      let sum = 0
      for (let i = 0; i < overallArr.length; i++) {
        sum += overallArr[i]
      }
      return (sum / overallArr.length).toFixed(1)
    },
    // 口味评分
    taste() {
      const tasteArr = Array.from(this.evaluate).map(item => item.taste)
      let sum = 0
      for (let i = 0; i < tasteArr.length; i++) {
        sum += tasteArr[i]
      }
      return (sum / tasteArr.length).toFixed(1)
    },
    // 环境评分
    environment() {
      const environmentArr = Array.from(this.evaluate).map(item => item.environment)
      let sum = 0
      for (let i = 0; i < environmentArr.length; i++) {
        sum += environmentArr[i]
      }
      return (sum / environmentArr.length).toFixed(1)
    },
    // 服务评分
    service() {
      const serviceArr = Array.from(this.evaluate).map(item => item.service)
      let sum = 0
      for (let i = 0; i < serviceArr.length; i++) {
        sum += serviceArr[i]
      }
      return (sum / serviceArr.length).toFixed(1)
    }
  }
}
</script>

<style lang="scss">
.comment {
  .swiper-box {
    .swiper-item {
      height: 100%;
      image {
        height: 100%;
        margin: 0 auto;
      }
    }
  }
  .main {
    padding: 50rpx;
    .address {
      display: flex;
      align-items: center;
      padding-bottom: 50rpx;
    }
    .rating {
      padding-top: 20rpx;
      text {
        padding-right: 20rpx;
      }
    }
    .title {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 50rpx 0 20rpx;
      .add {
        height: 50rpx;
        line-height: 50rpx;
        margin: 0;
        font-size: 24rpx;
        color: #fff;
        background-color: var(--themeColor);
      }
    }
    .content {
      .list {
        .item {
          padding: 50rpx 0;
          border-top: 1px dotted #eee;
          .head {
            display: flex;
            .avatar {
              width: 40px;
              height: 40px;
              border-radius: 10rpx;
            }
            .text {
              flex: 1;
              display: flex;
              flex-direction: column;
              justify-content: space-between;
              padding-left: 20rpx;
              .top {
                display: flex;
                justify-content: space-between;
                .name {
                  font-weight: 700;
                }
                .date {
                  font-size: 24rpx;
                  color: #999;
                }
              }
              .rate {
                display: flex;
                font-size: 24rpx;
                color: #999;
                .score {
                  color: #000;
                  padding: 0 20rpx 0 10rpx;
                }
              }
            }
          }
          .body {
            .comment {
              padding-top: 20rpx;
            }
            .images {
              padding-top: 20rpx;
              .image {
                display: inline-block;
                width: 210rpx;
                height: 210rpx;
                margin-right: 10rpx;
              }
              .image:last-child {
                margin-right: 0;
              }
            }
          }
        }
        .item:first-child {
          border-top: 1px solid #eee;
        }
      }
    }
  }
}
</style>
