// 修改资料
<template>
  <view class="user">
    <image class="avatar" :src="userForm.avatar" @click="changeAvatar"></image>
    <uni-forms ref="userRef" :modelValue="userForm" :rules="userRule" label-position="top">
      <uni-forms-item name="name" label="昵称">
        <uni-easyinput v-model="userForm.name" placeholder="请输入昵称" />
      </uni-forms-item>
      <uni-forms-item name="gender" label="性别">
        <uni-data-checkbox v-model="userForm.gender" :localdata="gender" />
      </uni-forms-item>
    </uni-forms>
    <button class="submit" @click="submit">保存</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      userForm: {
        avatar: '',
        name: '',
        gender: 0
      },
      userRule: {
        name: {
          rules: [
            { required: true, errorMessage: '请输入昵称' },
            { minLength: 1, maxLength: 10, errorMessage: '昵称长度在 1 到 10 个字符' }
          ]
        }
      },
      gender: [
        { value: 1, text: '男' },
        { value: 2, text: '女' },
        { value: 0, text: '保密' }
      ]
    }
  },
  methods: {
    // 获取用户信息
    retrieveUser() {
      wx.showLoading({
        title: '加载中',
        mask: true
      })
      wx.cloud
        .callFunction({
          name: 'retrieveUser'
        })
        .then(res => {
          const { avatar = res.result.data[0].avatarUrl, name = res.result.data[0].nickName, gender } = res.result.data[0]
          this.userForm.avatar = avatar
          this.userForm.name = name
          this.userForm.gender = gender
          wx.hideLoading()
        })
    },
    // 选择头像
    changeAvatar() {
      wx.chooseImage({
        count: 1
      })
        .then(res => {
          wx.cloud
            .uploadFile({
              cloudPath: `${Date.now()}.png`,
              filePath: res.tempFilePaths[0]
            })
            .then(res => {
              this.$set(this.userForm, 'avatar', res.fileID)
            })
        })
        .catch(err => {})
    },
    // 提交
    submit() {
      this.$refs.userRef
        .validate()
        .then(res => {
          wx.showLoading({
            title: '保存中',
            mask: true
          })
          wx.cloud
            .callFunction({
              name: 'updateInfo',
              data: this.userForm
            })
            .then(res => {
              wx.hideLoading()
              wx.showToast({
                title: '保存成功'
              })
              wx.cloud
                .callFunction({
                  name: 'retrieveUser'
                })
                .then(res => {
                  wx.setStorageSync('userInfo', res.result.data[0])
                  wx.hideToast()
                  wx.reLaunch({
                    url: '/pages/mine/mine'
                  })
                })
            })
        })
        .catch(err => {})
    }
  },
  onLoad() {
    this.retrieveUser()
  }
}
</script>

<style lang="scss">
.user {
  padding: 100rpx;
  .avatar {
    width: 200rpx;
    height: 200rpx;
    margin: 100rpx auto;
    display: block;
    border: 10rpx solid #ccc;
    border-radius: 50%;
  }
  .submit {
    background-color: var(--themeColor);
    color: #fff;
  }
}
</style>
