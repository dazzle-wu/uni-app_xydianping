// 周边商户详情
<template>
  <view class="detail">
    <ul class="info">
      <li class="name">
        <uni-title type="h1" :title="detail.name"></uni-title>
      </li>
      <li class="overall">
        <uni-rate size="18" readonly="true" :value="detail.detail_info.overall_rating" />
        <text v-if="detail.detail_info.price && detail.detail_info.price != 0">￥{{ detail.detail_info.price }}/人</text>
      </li>
      <li class="rate" v-if="detail.detail_info.taste_rating && detail.detail_info.taste_rating != 0">
        <text>口味:{{ detail.detail_info.taste_rating }}</text>
        <text>环境:{{ detail.detail_info.environment_rating }}</text>
        <text>服务:{{ detail.detail_info.service_rating }}</text>
      </li>
      <li class="shop" v-if="detail.detail_info.shop_hours">
        <text>营业时间：</text>
        <text class="hours">{{ detail.detail_info.shop_hours }}</text>
      </li>
      <li class="address" v-if="detail.address">
        <uni-icons type="location" size="18"></uni-icons>
        <text>{{ detail.address }}</text>
      </li>
      <li class="telephone" v-if="detail.telephone">
        <uni-icons type="phone" size="18"></uni-icons>
        <text>{{ detail.telephone }}</text>
      </li>
      <li class="atmosphere" v-if="detail.atmosphere">
        <text class="title">适合氛围</text>
        <view class="tags">
          <uni-tag :text="item" size="small" v-for="item in detail.atmosphere" :key="item"></uni-tag>
        </view>
      </li>
      <li class="featured" v-if="detail.featured">
        <text class="title">特色服务</text>
        <view class="tags">
          <uni-tag :text="item" size="small" v-for="item in detail.featured" :key="item"></uni-tag>
        </view>
      </li>
      <li class="content" v-if="detail.content">
        <text class="title">网友评价</text>
        <view class="tags">
          <uni-tag :text="item" size="small" v-for="item in detail.content" :key="item"></uni-tag>
        </view>
      </li>
      <li class="map">
        <image :src="detail.map"></image>
      </li>
      <li class="route" @click="navigate">
        <button>路线</button>
      </li>
    </ul>
  </view>
</template>

<script>
export default {
  data() {
    return {
      detail: {},
      school: '',
      lat: 0,
      lng: 0,
      latitude: 0,
      longitude: 0
    }
  },
  methods: {
    // 获取商户详情
    getDetail(uid) {
      this.request({
        url: `https://api.map.baidu.com/place/v2/detail?uid=${uid}&output=json&scope=2&ak=kuhiQDkUg888dssYESAMvgb83MMW8Oqm`
      }).then(res => {
        this.detail = res.result
        if (res.result.detail_info.atmosphere && res.result.detail_info.atmosphere.length) {
          this.$set(this.detail, 'atmosphere', this.unique(res.result.detail_info.atmosphere))
        }
        if (res.result.detail_info.featured_service && res.result.detail_info.featured_service.length) {
          this.$set(this.detail, 'featured', this.unique(res.result.detail_info.featured_service))
        }
        if (res.result.detail_info.content_tag) {
          this.$set(this.detail, 'content', res.result.detail_info.content_tag.split(';'))
        }
        this.$set(this.detail, 'map', `https://api.map.baidu.com/staticimage/v2?ak=kuhiQDkUg888dssYESAMvgb83MMW8Oqm&center=${res.result.location.lng},${res.result.location.lat}&zoom=15&copyright=1&markers=${res.result.location.lng},${res.result.location.lat}`)
        const { latitude, longitude } = this.bdMap_to_txMap(res.result.location.lat, res.result.location.lng)
        this.latitude = latitude
        this.longitude = longitude
      })
    },
    // 路线规划
    navigate() {
      const plugin = requirePlugin('routePlan')
      const key = 'SVRBZ-MI2WF-Q2FJC-JKQV3-OYACK-3KFZM' //使用在腾讯位置服务申请的key
      const referer = 'xydianping' //调用插件的app的名称
      const mode = 'walking'
      const startPoint = JSON.stringify({
        name: this.school,
        latitude: this.lat,
        longitude: this.lng
      })
      const endPoint = JSON.stringify({
        name: this.detail.name,
        latitude: this.latitude,
        longitude: this.longitude
      })
      wx.navigateTo({
        url: 'plugin://routePlan/index?key=' + key + '&referer=' + referer + '&mode=' + mode + '&startPoint=' + startPoint + '&endPoint=' + endPoint
      })
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
    },
    // 数组去重
    unique(arr) {
      for (let i = 0; i < arr.length; i++) {
        for (let j = i + 1; j < arr.length; j++) {
          if (arr[i] == arr[j]) {
            arr.splice(j, 1)
            j--
          }
        }
      }
      return arr
    }
  },
  onLoad(options) {
    this.getDetail(options.uid)
    this.school = options.school
    const { latitude, longitude } = this.bdMap_to_txMap(options.lat, options.lng)
    this.lat = latitude
    this.lng = longitude
  }
}
</script>

<style lang="scss">
.detail {
  padding: 50rpx;
  .info {
    .overall {
      display: flex;
      justify-content: space-between;
    }
    .rate {
      padding: 20rpx 5rpx;
      border-bottom: 1px solid #eee;
      text {
        padding-right: 20rpx;
      }
    }
    .shop {
      display: flex;
      padding: 20rpx 5rpx;
      border-bottom: 1px solid #eee;
      .hours {
        flex: 1;
      }
    }
    .address {
      padding: 20rpx 0;
      display: flex;
    }
    .telephone {
      padding-bottom: 20rpx;
    }
    .atmosphere,
    .featured,
    .content {
      padding: 20rpx 0;
      border-top: 1px solid #eee;
      .title {
        font-weight: 700;
        padding-left: 10rpx;
      }
      .tags {
        display: flex;
        flex-wrap: wrap;
        uni-tag {
          padding: 20rpx 10rpx 0;
          overflow: hidden;
          .uni-tag {
            max-width: 100%;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
          }
        }
      }
    }
    .route {
      padding-top: 20rpx;
      button {
        color: #fff;
        background-color: var(--themeColor);
      }
    }
  }
}
</style>
