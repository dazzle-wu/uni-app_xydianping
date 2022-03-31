// 添加商户
<template>
  <view class="business">
    <uni-forms label-position="top" ref="businessRef" :modelValue="businessForm" :rules="businessRule">
      <uni-forms-item name="signboard" label="门店照片">
        <uni-file-picker @select="add" @delete="remove" />
      </uni-forms-item>
      <uni-forms-item name="name" label="商户名">
        <uni-easyinput v-model="businessForm.name" placeholder="请填写商户招牌名称" />
      </uni-forms-item>
      <uni-forms-item name="type" label="商户类型">
        <uni-data-picker v-model="businessForm.type" :localdata="types" popup-title="请选择商户类型"></uni-data-picker>
      </uni-forms-item>
      <uni-forms-item name="address" label="地址">
        <uni-easyinput v-model="businessForm.address" placeholder="请填写商户地址" />
        <map class="map" :latitude="latitude" :longitude="longitude">
          <view class="mask"><button class="location" @click="chooseLocation">点击选择门店位置</button></view>
        </map>
      </uni-forms-item>
    </uni-forms>
    <button class="submit" @click="submit">提交</button>
  </view>
</template>
<script>
export default {
  data() {
    return {
      businessForm: {
        school: '',
        signboard: [],
        name: '',
        type: '',
        address: ''
      },
      businessRule: {
        signboard: {
          rules: [
            {
              required: true,
              errorMessage: '请上传门店照片'
            }
          ]
        },
        name: {
          rules: [
            {
              required: true,
              errorMessage: '请输入商户名'
            }
          ]
        },
        type: {
          rules: [
            {
              required: true,
              errorMessage: '请输入商户类型'
            }
          ]
        },
        address: {
          rules: [
            {
              required: true,
              errorMessage: '请输入地址'
            }
          ]
        }
      },
      types: [
        { text: '餐饮', value: '餐饮' },
        { text: '购物', value: '购物' },
        { text: '生活服务', value: '生活服务' },
        { text: '运动健身', value: '运动健身' }
      ],
      latitude: 0,
      longitude: 0
    }
  },
  methods: {
    // 获取学校经纬度
    getSchoolLocation(school) {
      this.request({
        url: `https://api.map.baidu.com/geocoding/v3/?address=${school}&output=json&ak=kuhiQDkUg888dssYESAMvgb83MMW8Oqm`
      }).then(res => {
        const { latitude, longitude } = this.bdMap_to_txMap(res.result.location.lat, res.result.location.lng)
        this.latitude = latitude
        this.longitude = longitude
      })
    },
    // 选择商户地址
    chooseLocation() {
      wx.chooseLocation({
        latitude: this.latitude,
        longitude: this.longitude
      }).then(res => {
        const { name = res.address, latitude, longitude } = res
        this.$set(this.businessForm, 'address', name)
        this.latitude = latitude
        this.longitude = longitude
      })
    },
    // 添加图片
    add(e) {
      this.businessForm.signboard.push(...e.tempFilePaths)
    },
    // 移除图片
    remove(e) {
      this.businessForm.signboard = this.businessForm.signboard.filter(item => item !== e.tempFilePath)
    },
    // 上传图片
    async upload() {
      const imgArr = []
      for (let i = 0; i < this.businessForm.signboard.length; i++) {
        await wx.cloud
          .uploadFile({
            cloudPath: `${Date.now()}.png`,
            filePath: this.businessForm.signboard[i]
          })
          .then(res => {
            imgArr[i] = res.fileID
          })
      }
      this.$set(this.businessForm, 'signboard', imgArr)
    },
    // 提交
    submit() {
      this.$refs.businessRef
        .validate()
        .then(async res => {
          wx.showLoading({
            title: '提交中',
            mask: true
          })
          await this.upload()
          wx.cloud
            .callFunction({
              name: 'createBusiness',
              data: this.businessForm
            })
            .then(res => {
              wx.hideLoading()
              wx.reLaunch({
                url: '/pages/school/school'
              })
            })
        })
        .catch(err => {})
    },
    // 百度地图经纬度转腾讯地图经纬度
    bdMap_to_txMap(lat, lng) {
      const pi = (3.14159265358979324 * 3000.0) / 180.0
      const x = lng - 0.0065
      const y = lat - 0.006
      const z = Math.sqrt(x * x + y * y) - 0.00002 * Math.sin(y * pi)
      const theta = Math.atan2(y, x) - 0.000003 * Math.cos(x * pi)
      const latitude = z * Math.sin(theta)
      const longitude = z * Math.cos(theta)
      return {
        latitude,
        longitude
      }
    }
  },
  onLoad() {
    const school = wx.getStorageSync('school')
    this.$set(this.businessForm, 'school', school)
    this.getSchoolLocation(school)
  }
}
</script>

<style lang="scss">
.business {
  padding: 80rpx;
  .map {
    position: relative;
    width: 100%;
    .mask {
      position: absolute;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
      background-color: #000;
      opacity: 0.5;
      .location {
        width: 250rpx;
        font-size: 24rpx;
        top: 50%;
        transform: translateY(-50%);
      }
    }
  }
  .submit {
    background-color: var(--themeColor);
    color: #fff;
  }
}
</style>
