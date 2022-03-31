// 周边
<template>
  <view class="near">
    <uni-data-picker v-model="query" :localdata="types" popup-title="请选择分类" @change="onchange"></uni-data-picker>
    <ul class="list">
      <li v-for="item in pois" :key="item.uid">
        <uni-card mode="title" isShadow="true" :title="item.name" :extra="item.detail_info.price && item.detail_info.price != 0 ? '参考价：￥' + item.detail_info.price : ''" note="true" @click="toDetail(item.uid, school, latitude, longitude)">
          <ul class="tags" v-if="item.detail_info.tag">
            <li class="tag" v-for="item1 in item.detail_info.tag.split(';')" :key="item1">
              <uni-tag :text="item1" size="small" type="primary"></uni-tag>
            </li>
          </ul>
          <uni-rate v-if="item.detail_info.overall_rating && item.detail_info.overall_rating != 0" readonly="true" :value="item.detail_info.overall_rating" />
          <uni-tag class="comment" v-if="item.detail_info.comment_num && item.detail_info.comment_num != 0" :text="item.detail_info.comment_num === '200' ? item.detail_info.comment_num + '+' : item.detail_info.comment_num" size="small" type="error" circle="true"></uni-tag>
          <template v-slot:footer>
            <view class="footer-box">
              <text class="address">{{ item.address }}</text>
              <text class="distance">{{ item.detail_info.distance }}米</text>
            </view>
          </template>
        </uni-card>
      </li>
    </ul>
    <uni-load-more :status="pageNum === totalNum ? 'noMore' : 'loading'"></uni-load-more>
  </view>
</template>

<script>
export default {
  data() {
    return {
      school: '',
      query: '美食',
      types: [
        { text: '美食', value: '美食' },
        { text: '购物', value: '购物' },
        { text: '生活服务', value: '生活服务' },
        { text: '运动健身', value: '运动健身' }
      ],
      latitude: 0,
      longitude: 0,
      total: 0,
      pageNum: 0,
      totalNum: 1,
      pois: []
    }
  },
  methods: {
    // 选择分类
    onchange() {
      this.scrollTop()
      this.pois = []
      this.pageNum = 0
      this.getPois(this.query)
    },
    // 获取我的大学经纬度
    getSchool(school) {
      this.request({
        url: `https://api.map.baidu.com/geocoding/v3/?address=${school}&output=json&ak=kuhiQDkUg888dssYESAMvgb83MMW8Oqm`
      }).then(res => {
        this.latitude = res.result.location.lat
        this.longitude = res.result.location.lng
        this.getPois(this.query)
      })
    },
    // 获取当前地理位置
    getLocation() {
      wx.getLocation({
        type: 'gcj02',
        isHighAccuracy: true
      }).then(res => {
        this.txMap_to_bdMap(res.latitude, res.longitude)
        this.getPois(this.query)
      })
    },
    // 获取商户列表
    getPois(query) {
      this.request({
        url: `https://api.map.baidu.com/place/v2/search?query=${query}&location=${this.latitude},${this.longitude}&output=json&scope=2&page_size=20&page_num=${this.pageNum}&ak=kuhiQDkUg888dssYESAMvgb83MMW8Oqm`
      }).then(res => {
        this.total = res.total
        this.totalNum = Math.ceil(res.total / 20)
        this.pois = [...this.pois, ...res.results]
        this.pageNum++
      })
    },
    // 跳转商户详情
    toDetail(uid, school, lat, lng) {
      wx.navigateTo({
        url: `/pages/near/detail?uid=${uid}&school=${school}&lat=${lat}&lng=${lng}`
      })
    },
    // 腾讯地图经纬度转百度地图经纬度
    txMap_to_bdMap(lat, lng) {
      const pi = (3.14159265358979324 * 3000.0) / 180.0
      const x = lng
      const y = lat
      const z = Math.sqrt(x * x + y * y) + 0.00002 * Math.sin(y * pi)
      const theta = Math.atan2(y, x) + 0.000003 * Math.cos(x * pi)
      const latitude = z * Math.sin(theta) + 0.006
      const longitude = z * Math.cos(theta) + 0.0065
      this.latitude = latitude
      this.longitude = longitude
    },
    // 回到顶部
    scrollTop() {
      wx.pageScrollTo({
        scrollTop: 0
      })
    }
  },
  onLoad() {
    if (wx.getStorageSync('school')) {
      this.school = wx.getStorageSync('school')
      this.getSchool(this.school)
    } else {
      this.school = '我的位置'
      this.getLocation()
    }
  },
  onReachBottom() {
    if (this.pageNum < this.totalNum) {
      this.getPois(this.query)
    }
  }
}
</script>

<style lang="scss">
.near {
  uni-data-picker {
    position: fixed;
    left: 0;
    top: 0;
    width: 100%;
    background-color: #fff;
    z-index: 999;
    .uni-data-tree {
      margin: 24rpx 30rpx;
    }
  }
  .list {
    margin-top: calc(40px + 48rpx);
    .tags .tag {
      display: inline;
      margin: 0 10rpx 0 0;
    }
    .uni-rate {
      margin: 10rpx 0 0;
    }
    .uni-tag {
      margin: 10rpx 0 0;
    }
    .footer-box {
      display: flex;
      font-size: 24rpx;
      color: #999;
      .address {
        flex: 1;
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
      }
      .distance {
        padding: 0 0 0 13px;
      }
    }
  }
}
</style>
