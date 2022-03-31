// 我的
<template>
  <view class="mine">
    <view class="top">
      <view v-if="!userInfo.openid">
        <button class="login" lang="zh_CN" @click="getUserProfile">登录</button>
      </view>
      <view class="uploader-container" v-if="userInfo.openid">
        <image class="avatar" :src="userInfo.avatar || userInfo.avatarUrl"></image>
        <view class="name">{{ userInfo.name || userInfo.nickName }}</view>
      </view>
    </view>
    <uni-grid class="bottom" @change="skip()">
      <uni-grid-item index="0">
        <view class="item">
          <uni-icons type="person" size="30"></uni-icons>
          <text>修改资料</text>
        </view>
      </uni-grid-item>
      <uni-grid-item index="1">
        <view class="item">
          <uni-icons type="map-pin-ellipse" size="30"></uni-icons>
          <text>我的大学</text>
        </view>
      </uni-grid-item>
      <uni-grid-item index="2">
        <view class="item">
          <uni-icons type="star" size="30"></uni-icons>
          <text>我的收藏</text>
        </view>
      </uni-grid-item>
      <uni-grid-item index="3">
        <view class="item">
          <uni-icons type="compose" size="30"></uni-icons>
          <text>建议反馈</text>
        </view>
      </uni-grid-item>
      <uni-grid-item index="4">
        <view class="item">
          <uni-icons type="email" size="30"></uni-icons>
          <text>我的反馈</text>
        </view>
      </uni-grid-item>
      <uni-grid-item index="5">
        <view class="item">
          <uni-icons type="close" size="30"></uni-icons>
          <text>退出登录</text>
        </view>
      </uni-grid-item>
    </uni-grid>
  </view>
</template>

<script>
export default {
  data() {
    return {
      userInfo: {}
    }
  },
  methods: {
    // 获取用户信息
    getUserProfile() {
      wx.getUserProfile({
        desc: '用于完善会员资料',
        success: res => {
          wx.showLoading({
            title: '登录中',
            mask: true
          })
          wx.cloud.callFunction({
            name: 'login',
            success: res1 => {
              this.userInfo = res.userInfo
              this.$set(this.userInfo, 'openid', res1.result.openid)
              wx.cloud
                .callFunction({
                  name: 'retrieveUser'
                })
                .then(res => {
                  if (res.result.data.length) {
                    wx.cloud
                      .callFunction({
                        name: 'updateUser',
                        data: this.userInfo
                      })
                      .then(res => {
                        wx.cloud
                          .callFunction({
                            name: 'retrieveUser'
                          })
                          .then(res => {
                            this.userInfo = res.result.data[0]
                            wx.setStorageSync('userInfo', this.userInfo)
                          })
                      })
                    wx.cloud
                      .callFunction({
                        name: 'getLike'
                      })
                      .then(res => {
                        if (res.result.data.length) {
                          wx.setStorageSync('likes', res.result.data[0].likes)
                        }
                      })
                    wx.cloud
                      .callFunction({
                        name: 'getCollect'
                      })
                      .then(res => {
                        if (res.result.data.length) {
                          wx.setStorageSync('collects', res.result.data[0].collects)
                        }
                      })
                    wx.cloud
                      .callFunction({
                        name: 'getCollege'
                      })
                      .then(res => {
                        if (res.result.data.length) {
                          wx.setStorageSync('school', res.result.data[0].college + res.result.data[0].campus)
                          wx.hideLoading()
                          wx.reLaunch({
                            url: '/pages/school/school'
                          })
                        } else {
                          wx.hideLoading()
                          wx.navigateTo({
                            url: '/pages/mine/location'
                          })
                        }
                      })
                  } else {
                    wx.cloud
                      .callFunction({
                        name: 'createUser',
                        data: this.userInfo
                      })
                      .then(res => {
                        wx.setStorageSync('userInfo', this.userInfo)
                        wx.hideLoading()
                        wx.navigateTo({
                          url: '/pages/mine/location'
                        })
                      })
                  }
                })
            }
          })
        }
      })
    },
    // 退出登录
    logout() {
      wx.showModal({
        title: '提示',
        content: '你确定要退出登录吗'
      }).then(res => {
        if (res.confirm) {
          this.userInfo = {}
          wx.clearStorageSync()
          wx.reLaunch({
            url: '/pages/school/school'
          })
        }
      })
    },
    // 跳转到相应页面
    skip(e) {
      if (!wx.getStorageSync('userInfo')) {
        wx.showModal({
          title: '提示',
          content: '请先登录'
        })
      } else {
        switch (e.detail.index) {
          case 0:
            wx.navigateTo({
              url: '/pages/mine/user'
            })
            break
          case 1:
            wx.navigateTo({
              url: '/pages/mine/location'
            })
            break
          case 2:
            wx.navigateTo({
              url: '/pages/mine/collection'
            })
            break
          case 3:
            wx.navigateTo({
              url: '/pages/mine/feedback'
            })
            break
          case 4:
            wx.navigateTo({
              url: '/pages/mine/reply'
            })
            break
          case 5:
            this.logout()
            break
        }
      }
    }
  },
  onLoad() {
    const ui = wx.getStorageSync('userInfo')
    this.userInfo = ui
  }
}
</script>

<style lang="scss">
.top {
  .login {
    background-color: var(--themeColor);
    width: 60%;
    margin: 200rpx auto;
    color: #fff;
  }
  .uploader-container {
    margin: 100rpx 0;
    .avatar {
      width: 200rpx;
      height: 200rpx;
      margin: 0 auto;
      display: block;
      border-radius: 50%;
    }
    .name {
      text-align: center;
      margin-top: 10rpx;
    }
  }
}
.bottom {
  .uni-grid-wrap {
    padding: 30rpx;
  }
  uni-grid-item {
    text-align: center;
    .item {
      height: 100%;
      display: flex;
      flex-direction: column;
      justify-content: center;
    }
  }
}
</style>
