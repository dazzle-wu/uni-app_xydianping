// 校内
<template>
  <view class="school">
    <uni-notice-bar v-if="notice1" single="true" showClose="true" text="暂时没有商户数据，点击加号以添加商户"></uni-notice-bar>
    <uni-notice-bar v-if="notice2" single="true" showClose="true" text="你还没有设置学校，登录以设置我的大学"></uni-notice-bar>
    <ul>
      <li v-for="(item, index) in businessList" :key="item._id">
        <uni-card mode="style" :is-shadow="true" :thumbnail="item.signboard[0]" :extra="item.name" :note="item.address" @click="toComment(item._id)">
          <template v-slot:footer>
            <view class="footer-box">
              <view @click="like(item._id, index)">
                <uni-icons :type="likes.includes(item._id) ? 'hand-thumbsup-filled' : 'hand-thumbsup'"></uni-icons>
                <text v-if="item.like.length">({{ item.like.length }})</text>
              </view>
              <view @click="toComment(item._id)">
                <uni-icons type="chat"></uni-icons>
              </view>
              <view @click="collect(item._id, index)">
                <uni-icons :type="collects.includes(item._id) ? 'heart-filled' : 'heart'"></uni-icons>
                <text v-if="item.collect.length">({{ item.collect.length }})</text>
              </view>
            </view>
          </template>
        </uni-card>
      </li>
    </ul>
    <uni-fab horizontal="right" @fabClick="add"></uni-fab>
  </view>
</template>

<script>
export default {
  data() {
    return {
      notice1: false,
      notice2: false,
      businessList: [],
      likes: [],
      collects: []
    }
  },
  methods: {
    // 添加商户
    add() {
      if (!wx.getStorageSync('userInfo')) {
        wx.showModal({
          title: '提示',
          content: '请先登录'
        }).then(res => {
          if (res.confirm)
            wx.switchTab({
              url: '/pages/mine/mine'
            })
        })
      } else if (!wx.getStorageSync('school')) {
        wx.showModal({
          title: '提示',
          content: '请先设置学校'
        }).then(res => {
          if (res.confirm)
            wx.navigateTo({
              url: '/pages/mine/location'
            })
        })
      } else {
        wx.navigateTo({
          url: '/pages/school/business'
        })
      }
    },
    // 获取商户列表
    retrieveBusiness(school) {
      wx.cloud
        .callFunction({
          name: 'retrieveBusiness',
          data: {
            school
          }
        })
        .then(res => {
          if (res.result.data.length) {
            this.businessList = res.result.data
          } else {
            this.notice1 = true
          }
        })
    },
    // 跳转商户详情
    toComment(id) {
      wx.navigateTo({
        url: `/pages/school/comment?id=${id}`
      })
    },
    // 点赞
    like(id, index) {
      if (!wx.getStorageSync('userInfo')) {
        wx.showModal({
          title: '提示',
          content: '请先登录'
        }).then(res => {
          if (res.confirm)
            wx.switchTab({
              url: '/pages/mine/mine'
            })
        })
      } else {
        if (!this.likes.includes(id)) {
          this.likes.push(id)
          this.businessList[index].like.push(wx.getStorageSync('userInfo').openid)
          wx.cloud.callFunction({
            name: 'like',
            data: {
              id
            }
          })
        } else {
          this.likes = this.likes.filter(item => item !== id)
          this.businessList[index].like = this.businessList[index].like.filter(item => item !== wx.getStorageSync('userInfo').openid)
          wx.cloud.callFunction({
            name: 'cancelLike',
            data: {
              id
            }
          })
        }
        wx.cloud.callFunction({
          name: 'setLike',
          data: {
            likes: this.likes
          }
        })
        wx.setStorageSync('likes', this.likes)
      }
    },
    // 收藏
    collect(id, index) {
      if (!wx.getStorageSync('userInfo')) {
        wx.showModal({
          title: '提示',
          content: '请先登录'
        }).then(res => {
          if (res.confirm) {
            wx.switchTab({
              url: '/pages/mine/mine'
            })
          }
        })
      } else {
        if (!this.collects.includes(id)) {
          this.collects.push(id)
          this.businessList[index].collect.push(wx.getStorageSync('userInfo').openid)
          wx.cloud.callFunction({
            name: 'collect',
            data: {
              id
            }
          })
        } else {
          this.collects = this.collects.filter(item => item !== id)
          this.businessList[index].collect = this.businessList[index].collect.filter(item => item !== wx.getStorageSync('userInfo').openid)
          wx.cloud.callFunction({
            name: 'cancelCollect',
            data: {
              id
            }
          })
        }
        wx.cloud.callFunction({
          name: 'setCollect',
          data: {
            collects: this.collects
          }
        })
        wx.setStorageSync('collects', this.collects)
      }
    }
  },
  onLoad() {
    if (wx.getStorageSync('likes')) {
      this.likes = wx.getStorageSync('likes')
    }
    if (wx.getStorageSync('collects')) {
      this.collects = wx.getStorageSync('collects')
    }
    if (!wx.getStorageSync('school')) {
      this.notice2 = true
      this.retrieveBusiness('广州中医药大学大学城校区')
      wx.setNavigationBarTitle({
        title: '广州中医药大学大学城校区'
      })
    } else {
      const school = wx.getStorageSync('school')
      this.retrieveBusiness(school)
      wx.setNavigationBarTitle({
        title: school
      })
    }
  }
}
</script>

<style lang="scss">
.school {
  .uni-card {
    .uni-card__content-extra {
      color: #000;
    }
    .footer-box {
      display: flex;
      view {
        flex: 1;
        text-align: center;
      }
    }
  }
}
</style>
