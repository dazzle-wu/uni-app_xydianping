// 我的大学
<template>
  <view class="location">
    <uni-forms ref="schoolRef" :v-model="schoolForm" label-position="top" label-width="150">
      <uni-forms-item name="location" label="所在地（主校区）">
        <uni-data-picker v-model="schoolForm.location" :localdata="citys" popup-title="请选择所在地" @change="retrieveSchool"></uni-data-picker>
      </uni-forms-item>
      <uni-forms-item name="school" label="学校">
        <uni-data-picker :readonly="!schoolForm.location" v-model="schoolForm.school" :localdata="schools" popup-title="请选择学校" @change="retrieveArea"></uni-data-picker>
      </uni-forms-item>
      <uni-forms-item v-if="requireArea" name="area" label="校区">
        <uni-data-picker :readonly="!requireArea" v-model="schoolForm.area" :localdata="areas" popup-title="请选择校区"></uni-data-picker>
      </uni-forms-item>
    </uni-forms>
    <button class="submit" :disabled="schoolForm.school === '' || (requireArea === true && schoolForm.area === '')" @click="submit">保存</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      citys: [
        {
          text: '安徽省',
          value: '安徽省',
          children: [
            { text: '安庆市', value: '安庆市' },
            { text: '蚌埠市', value: '蚌埠市' },
            { text: '亳州市', value: '亳州市' },
            { text: '池州市', value: '池州市' },
            { text: '滁州市', value: '滁州市' },
            { text: '阜阳市', value: '阜阳市' },
            { text: '合肥市', value: '合肥市' },
            { text: '淮北市', value: '淮北市' },
            { text: '淮南市', value: '淮南市' },
            { text: '黄山市', value: '黄山市' },
            { text: '六安市', value: '六安市' },
            { text: '马鞍山市', value: '马鞍山市' },
            { text: '宿州市', value: '宿州市' },
            { text: '铜陵市', value: '铜陵市' },
            { text: '芜湖市', value: '芜湖市' },
            { text: '宣城市', value: '宣城市' }
          ]
        },
        { text: '北京市', value: '北京市' },
        { text: '重庆市', value: '重庆市' },
        {
          text: '福建省',
          value: '福建省',
          children: [
            { text: '福州市', value: '福州市' },
            { text: '龙岩市', value: '龙岩市' },
            { text: '南平市', value: '南平市' },
            { text: '宁德市', value: '宁德市' },
            { text: '莆田市', value: '莆田市' },
            { text: '泉州市', value: '泉州市' },
            { text: '三明市', value: '三明市' },
            { text: '厦门市', value: '厦门市' },
            { text: '漳州市', value: '漳州市' }
          ]
        },
        {
          text: '甘肃省',
          value: '甘肃省',
          children: [
            { text: '白银市', value: '白银市' },
            { text: '定西市', value: '定西市' },
            { text: '甘南藏族自治州', value: '甘南藏族自治州' },
            { text: '嘉峪关市', value: '嘉峪关市' },
            { text: '金昌市', value: '金昌市' },
            { text: '酒泉市', value: '酒泉市' },
            { text: '兰州市', value: '兰州市' },
            { text: '临夏州', value: '临夏州' },
            { text: '陇南市', value: '陇南市' },
            { text: '平凉市', value: '平凉市' },
            { text: '庆阳市', value: '庆阳市' },
            { text: '天水市', value: '天水市' },
            { text: '武威市', value: '武威市' },
            { text: '张掖市', value: '张掖市' }
          ]
        },
        {
          text: '广东省',
          value: '广东省',
          children: [
            { text: '潮州市', value: '潮州市' },
            { text: '东莞市', value: '东莞市' },
            { text: '佛山市', value: '佛山市' },
            { text: '广州市', value: '广州市' },
            { text: '河源市', value: '河源市' },
            { text: '惠州市', value: '惠州市' },
            { text: '江门市', value: '江门市' },
            { text: '揭阳市', value: '揭阳市' },
            { text: '茂名市', value: '茂名市' },
            { text: '梅州市', value: '梅州市' },
            { text: '清远市', value: '清远市' },
            { text: '汕头市', value: '汕头市' },
            { text: '汕尾市', value: '汕尾市' },
            { text: '韶关市', value: '韶关市' },
            { text: '深圳市', value: '深圳市' },
            { text: '阳江市', value: '阳江市' },
            { text: '云浮市', value: '云浮市' },
            { text: '湛江市', value: '湛江市' },
            { text: '肇庆市', value: '肇庆市' },
            { text: '中山市', value: '中山市' },
            { text: '珠海市', value: '珠海市' }
          ]
        },
        {
          text: '广西壮族自治区',
          value: '广西壮族自治区',
          children: [
            { text: '百色市', value: '百色市' },
            { text: '北海市', value: '北海市' },
            { text: '崇左市', value: '崇左市' },
            { text: '防城港市', value: '防城港市' },
            { text: '贵港市', value: '贵港市' },
            { text: '桂林市', value: '桂林市' },
            { text: '河池市', value: '河池市' },
            { text: '贺州市', value: '贺州市' },
            { text: '来宾市', value: '来宾市' },
            { text: '柳州市', value: '柳州市' },
            { text: '南宁市', value: '南宁市' },
            { text: '钦州市', value: '钦州市' },
            { text: '梧州市', value: '梧州市' },
            { text: '玉林市', value: '玉林市' }
          ]
        },
        {
          text: '贵州省',
          value: '贵州省',
          children: [
            { text: '安顺市', value: '安顺市' },
            { text: '毕节市', value: '毕节市' },
            { text: '贵阳市', value: '贵阳市' },
            { text: '六盘水市', value: '六盘水市' },
            { text: '黔东南苗族侗族自治州', value: '黔东南苗族侗族自治州' },
            { text: '黔西南布依族苗族自治州', value: '黔西南布依族苗族自治州' },
            { text: '铜仁市', value: '铜仁市' },
            { text: '遵义市', value: '遵义市' }
          ]
        },
        {
          text: '海南省',
          value: '海南省',
          children: [
            { text: '海口市', value: '海口市' },
            { text: '琼海市', value: '琼海市' },
            { text: '三亚市', value: '三亚市' },
            { text: '文昌市', value: '文昌市' }
          ]
        },
        {
          text: '河北省',
          value: '河北省',
          children: [
            { text: '保定市', value: '保定市' },
            { text: '沧州市', value: '沧州市' },
            { text: '承德市', value: '承德市' },
            { text: '邯郸市', value: '邯郸市' },
            { text: '衡水市', value: '衡水市' },
            { text: '廊坊市', value: '廊坊市' },
            { text: '秦皇岛市', value: '秦皇岛市' },
            { text: '石家庄市', value: '石家庄市' },
            { text: '唐山市', value: '唐山市' },
            { text: '邢台市', value: '邢台市' },
            { text: '张家口市', value: '张家口市' }
          ]
        },
        {
          text: '河南省',
          value: '河南省',
          children: [
            { text: '安阳市', value: '安阳市' },
            { text: '鹤壁市', value: '鹤壁市' },
            { text: '济源市', value: '济源市' },
            { text: '焦作市', value: '焦作市' },
            { text: '开封市', value: '开封市' },
            { text: '洛阳市', value: '洛阳市' },
            { text: '漯河市', value: '漯河市' },
            { text: '南阳市', value: '南阳市' },
            { text: '平顶山市', value: '平顶山市' },
            { text: '濮阳市', value: '濮阳市' },
            { text: '三门峡市', value: '三门峡市' },
            { text: '商丘市', value: '商丘市' },
            { text: '新乡市', value: '新乡市' },
            { text: '信阳市', value: '信阳市' },
            { text: '许昌市', value: '许昌市' },
            { text: '郑州市', value: '郑州市' },
            { text: '周口市', value: '周口市' },
            { text: '驻马店市', value: '驻马店市' }
          ]
        },
        {
          text: '黑龙江省',
          value: '黑龙江省',
          children: [
            { text: '大庆市', value: '大庆市' },
            { text: '大兴安岭地区', value: '大兴安岭地区' },
            { text: '哈尔滨市', value: '哈尔滨市' },
            { text: '鹤岗市', value: '鹤岗市' },
            { text: '黑河市', value: '黑河市' },
            { text: '鸡西市', value: '鸡西市' },
            { text: '佳木斯市', value: '佳木斯市' },
            { text: '牡丹江市', value: '牡丹江市' },
            { text: '七台河市', value: '七台河市' },
            { text: '齐齐哈尔市', value: '齐齐哈尔市' },
            { text: '双鸭山市', value: '双鸭山市' },
            { text: '绥化市', value: '绥化市' },
            { text: '伊春市', value: '伊春市' }
          ]
        },
        {
          text: '湖北省',
          value: '湖北省',
          children: [
            { text: '鄂州市', value: '鄂州市' },
            { text: '恩施土家族苗族自治州', value: '恩施土家族苗族自治州' },
            { text: '黄冈市', value: '黄冈市' },
            { text: '黄石市', value: '黄石市' },
            { text: '荆门市', value: '荆门市' },
            { text: '荆州市', value: '荆州市' },
            { text: '潜江市', value: '潜江市' },
            { text: '十堰市', value: '十堰市' },
            { text: '随州市', value: '随州市' },
            { text: '天门市', value: '天门市' },
            { text: '武汉市', value: '武汉市' },
            { text: '仙桃市', value: '仙桃市' },
            { text: '咸宁市', value: '咸宁市' },
            { text: '襄阳市', value: '襄阳市' },
            { text: '孝感市', value: '孝感市' },
            { text: '宜昌市', value: '宜昌市' }
          ]
        },
        {
          text: '湖南省',
          value: '湖南省',
          children: [
            { text: '常德市', value: '常德市' },
            { text: '郴州市', value: '郴州市' },
            { text: '衡阳市', value: '衡阳市' },
            { text: '怀化市', value: '怀化市' },
            { text: '娄底市', value: '娄底市' },
            { text: '邵阳市', value: '邵阳市' },
            { text: '湘潭市', value: '湘潭市' },
            { text: '湘西土家族苗族自治州', value: '湘西土家族苗族自治州' },
            { text: '益阳市', value: '益阳市' },
            { text: '永州市', value: '永州市' },
            { text: '岳阳市', value: '岳阳市' },
            { text: '张家界市', value: '张家界市' },
            { text: '长沙市', value: '长沙市' },
            { text: '株洲市', value: '株洲市' }
          ]
        },
        {
          text: '吉林省',
          value: '吉林省',
          children: [
            { text: '白城市', value: '白城市' },
            { text: '白山市', value: '白山市' },
            { text: '长春市', value: '长春市' },
            { text: '吉林市', value: '吉林市' },
            { text: '辽源市', value: '辽源市' },
            { text: '梅河口市', value: '梅河口市' },
            { text: '四平市', value: '四平市' },
            { text: '松原市', value: '松原市' },
            { text: '通化市', value: '通化市' },
            { text: '延边朝鲜族自治州', value: '延边朝鲜族自治州' }
          ]
        },
        {
          text: '江苏省',
          value: '江苏省',
          children: [
            { text: '常州市', value: '常州市' },
            { text: '淮安市', value: '淮安市' },
            { text: '昆山市', value: '昆山市' },
            { text: '连云港市', value: '连云港市' },
            { text: '南京市', value: '南京市' },
            { text: '南通市', value: '南通市' },
            { text: '苏州市', value: '苏州市' },
            { text: '宿迁市', value: '宿迁市' },
            { text: '泰州市', value: '泰州市' },
            { text: '无锡市', value: '无锡市' },
            { text: '徐州市', value: '徐州市' },
            { text: '盐城市', value: '盐城市' },
            { text: '扬州市', value: '扬州市' },
            { text: '镇江市', value: '镇江市' }
          ]
        },
        {
          text: '江西省',
          value: '江西省',
          children: [
            { text: '抚州市', value: '抚州市' },
            { text: '赣州市', value: '赣州市' },
            { text: '吉安市', value: '吉安市' },
            { text: '景德镇市', value: '景德镇市' },
            { text: '九江市', value: '九江市' },
            { text: '南昌市', value: '南昌市' },
            { text: '萍乡市', value: '萍乡市' },
            { text: '上饶市', value: '上饶市' },
            { text: '新余市', value: '新余市' },
            { text: '宜春市', value: '宜春市' },
            { text: '鹰潭市', value: '鹰潭市' }
          ]
        },
        {
          text: '辽宁省',
          value: '辽宁省',
          children: [
            { text: '鞍山市', value: '鞍山市' },
            { text: '本溪市', value: '本溪市' },
            { text: '朝阳市', value: '朝阳市' },
            { text: '大连市', value: '大连市' },
            { text: '丹东市', value: '丹东市' },
            { text: '抚顺市', value: '抚顺市' },
            { text: '阜新市', value: '阜新市' },
            { text: '葫芦岛市', value: '葫芦岛市' },
            { text: '锦州市', value: '锦州市' },
            { text: '辽阳市', value: '辽阳市' },
            { text: '盘锦市', value: '盘锦市' },
            { text: '沈阳市', value: '沈阳市' },
            { text: '铁岭市', value: '铁岭市' },
            { text: '营口市', value: '营口市' }
          ]
        },
        {
          text: '内蒙古自治区',
          value: '内蒙古自治区',
          children: [
            { text: '阿拉善盟', value: '阿拉善盟' },
            { text: '巴彦淖尔市', value: '巴彦淖尔市' },
            { text: '包头市', value: '包头市' },
            { text: '赤峰市', value: '赤峰市' },
            { text: '鄂尔多斯市', value: '鄂尔多斯市' },
            { text: '呼和浩特市', value: '呼和浩特市' },
            { text: '呼伦贝尔市', value: '呼伦贝尔市' },
            { text: '通辽市', value: '通辽市' },
            { text: '乌海市', value: '乌海市' },
            { text: '乌兰察布市', value: '乌兰察布市' },
            { text: '锡林郭勒盟', value: '锡林郭勒盟' },
            { text: '兴安盟', value: '兴安盟' }
          ]
        },
        {
          text: '宁夏回族自治区',
          value: '宁夏回族自治区',
          children: [
            { text: '固原市', value: '固原市' },
            { text: '石嘴山市', value: '石嘴山市' },
            { text: '吴忠市', value: '吴忠市' },
            { text: '银川市', value: '银川市' }
          ]
        },
        {
          text: '青海省',
          value: '青海省',
          children: [
            { text: '海东市', value: '海东市' },
            { text: '海西蒙古族藏族自治州', value: '海西蒙古族藏族自治州' },
            { text: '西宁市', value: '西宁市' }
          ]
        },
        {
          text: '山东省',
          value: '山东省',
          children: [
            { text: '滨州市', value: '滨州市' },
            { text: '德州市', value: '德州市' },
            { text: '东营市', value: '东营市' },
            { text: '菏泽市', value: '菏泽市' },
            { text: '济南市', value: '济南市' },
            { text: '济宁市', value: '济宁市' },
            { text: '莱芜市', value: '莱芜市' },
            { text: '聊城市', value: '聊城市' },
            { text: '临沂市', value: '临沂市' },
            { text: '青岛市', value: '青岛市' },
            { text: '日照市', value: '日照市' },
            { text: '泰安市', value: '泰安市' },
            { text: '威海市', value: '威海市' },
            { text: '潍坊市', value: '潍坊市' },
            { text: '烟台市', value: '烟台市' },
            { text: '枣庄市', value: '枣庄市' },
            { text: '淄博市', value: '淄博市' }
          ]
        },
        {
          text: '山西省',
          value: '山西省',
          children: [
            { text: '大同市', value: '大同市' },
            { text: '晋城市', value: '晋城市' },
            { text: '晋中市', value: '晋中市' },
            { text: '临汾市', value: '临汾市' },
            { text: '吕梁市', value: '吕梁市' },
            { text: '朔州市', value: '朔州市' },
            { text: '太原市', value: '太原市' },
            { text: '忻州市', value: '忻州市' },
            { text: '阳泉市', value: '阳泉市' },
            { text: '运城市', value: '运城市' },
            { text: '长治市', value: '长治市' }
          ]
        },
        {
          text: '陕西省',
          value: '陕西省',
          children: [
            { text: '安康市', value: '安康市' },
            { text: '宝鸡市', value: '宝鸡市' },
            { text: '汉中市', value: '汉中市' },
            { text: '商洛市', value: '商洛市' },
            { text: '神木市', value: '神木市' },
            { text: '铜川市', value: '铜川市' },
            { text: '渭南市', value: '渭南市' },
            { text: '西安市', value: '西安市' },
            { text: '咸阳市', value: '咸阳市' },
            { text: '延安市', value: '延安市' },
            { text: '榆林市', value: '榆林市' }
          ]
        },
        { text: '上海市', value: '上海市' },
        {
          text: '四川省',
          value: '四川省',
          children: [
            { text: '阿坝藏族羌族自治州', value: '阿坝藏族羌族自治州' },
            { text: '巴中市', value: '巴中市' },
            { text: '成都市', value: '成都市' },
            { text: '达州市', value: '达州市' },
            { text: '德阳市', value: '德阳市' },
            { text: '甘孜藏族自治州', value: '甘孜藏族自治州' },
            { text: '广安市', value: '广安市' },
            { text: '广元市', value: '广元市' },
            { text: '乐山市', value: '乐山市' },
            { text: '凉山彝族自治州', value: '凉山彝族自治州' },
            { text: '泸州市', value: '泸州市' },
            { text: '眉山市', value: '眉山市' },
            { text: '绵阳市', value: '绵阳市' },
            { text: '南充市', value: '南充市' },
            { text: '内江市', value: '内江市' },
            { text: '攀枝花市', value: '攀枝花市' },
            { text: '遂宁市', value: '遂宁市' },
            { text: '雅安市', value: '雅安市' },
            { text: '宜宾市', value: '宜宾市' },
            { text: '资阳市', value: '资阳市' },
            { text: '自贡市', value: '自贡市' }
          ]
        },
        { text: '天津市', value: '天津市' },
        {
          text: '西藏自治区',
          value: '西藏自治区',
          children: [
            { text: '拉萨市', value: '拉萨市' },
            { text: '林芝市', value: '林芝市' }
          ]
        },
        {
          text: '新疆维吾尔自治区',
          value: '新疆维吾尔自治区',
          children: [
            { text: '阿克苏地区', value: '阿克苏地区' },
            { text: '阿拉尔市', value: '阿拉尔市' },
            { text: '阿勒泰地区', value: '阿勒泰地区' },
            { text: '巴音郭楞蒙古自治州', value: '巴音郭楞蒙古自治州' },
            { text: '博尔塔拉蒙古自治州', value: '博尔塔拉蒙古自治州' },
            { text: '昌吉回族自治州', value: '昌吉回族自治州' },
            { text: '哈密市', value: '哈密市' },
            { text: '和田地区', value: '和田地区' },
            { text: '喀什地区', value: '喀什地区' },
            { text: '克拉玛依市', value: '克拉玛依市' },
            { text: '克孜勒苏柯尔克孜自治州', value: '克孜勒苏柯尔克孜自治州' },
            { text: '石河子市', value: '石河子市' },
            { text: '塔城地区', value: '塔城地区' },
            { text: '铁门关市', value: '铁门关市' },
            { text: '图木舒克市', value: '图木舒克市' },
            { text: '吐鲁番市', value: '吐鲁番市' },
            { text: '乌鲁木齐市', value: '乌鲁木齐市' },
            { text: '五家渠市', value: '五家渠市' },
            { text: '伊犁哈萨克自治州', value: '伊犁哈萨克自治州' }
          ]
        },
        {
          text: '云南省',
          value: '云南省',
          children: [
            { text: '保山市', value: '保山市' },
            { text: '楚雄彝族自治州', value: '楚雄彝族自治州' },
            { text: '大理白族自治州', value: '大理白族自治州' },
            { text: '大理市', value: '大理市' },
            { text: '德宏傣族景颇族自治州', value: '德宏傣族景颇族自治州' },
            { text: '红河哈尼族彝族自治州', value: '红河哈尼族彝族自治州' },
            { text: '红河州', value: '红河州' },
            { text: '昆明市', value: '昆明市' },
            { text: '丽江市', value: '丽江市' },
            { text: '临沧市', value: '临沧市' },
            { text: '普洱市', value: '普洱市' },
            { text: '曲靖市', value: '曲靖市' },
            { text: '文山壮族苗族自治州', value: '文山壮族苗族自治州' },
            { text: '西双版纳傣族自治州', value: '西双版纳傣族自治州' },
            { text: '玉溪市', value: '玉溪市' },
            { text: '昭通市', value: '昭通市' }
          ]
        },
        {
          text: '浙江省',
          value: '浙江省',
          children: [
            { text: '杭州市', value: '杭州市' },
            { text: '湖州市', value: '湖州市' },
            { text: '嘉兴市', value: '嘉兴市' },
            { text: '金华市', value: '金华市' },
            { text: '丽水市', value: '丽水市' },
            { text: '宁波市', value: '宁波市' },
            { text: '衢州市', value: '衢州市' },
            { text: '绍兴市', value: '绍兴市' },
            { text: '台州市', value: '台州市' },
            { text: '温州市', value: '温州市' },
            { text: '舟山市', value: '舟山市' }
          ]
        }
      ],
      schools: [],
      areas: [],
      requireArea: false,
      schoolForm: {
        location: '',
        school: '',
        area: ''
      }
    }
  },
  methods: {
    // 获取我的大学
    getCollege() {
      wx.showLoading({
        title: '加载中',
        mask: true
      })
      wx.cloud
        .callFunction({
          name: 'getCollege'
        })
        .then(res => {
          if (res.result.data.length) {
            this.$set(this.schoolForm, 'location', res.result.data[0].city)
            this.retrieveSchool()
            this.$set(this.schoolForm, 'school', res.result.data[0].college)
            this.retrieveArea()
            this.$set(this.schoolForm, 'area', res.result.data[0].campus)
          }
          wx.hideLoading()
        })
    },
    // 获取大学列表
    retrieveSchool() {
      this.$set(this.schoolForm, 'area', '')
      this.areas = []
      this.requireArea = false
      this.$set(this.schoolForm, 'school', '')
      this.schools = []
      wx.cloud
        .callFunction({
          name: 'retrieveSchool',
          data: {
            city: this.schoolForm.location
          }
        })
        .then(res => {
          const schools = res.result.data.map(item => item.school)
          for (let i = 0; i < schools.length; i++) {
            let school = {}
            this.$set(school, 'text', schools[i])
            this.$set(school, 'value', schools[i])
            this.schools.push(school)
          }
        })
    },
    // 获取校区列表
    retrieveArea() {
      this.$set(this.schoolForm, 'area', '')
      this.areas = []
      wx.cloud
        .callFunction({
          name: 'retrieveArea',
          data: {
            school: this.schoolForm.school
          }
        })
        .then(res => {
          if (res.result.data[0].hasOwnProperty('area')) {
            this.requireArea = true
            const areas = res.result.data[0].area
            for (let i = 0; i < areas.length; i++) {
              let area = {}
              this.$set(area, 'text', areas[i])
              this.$set(area, 'value', areas[i])
              this.areas.push(area)
            }
          } else {
            this.requireArea = false
          }
        })
    },
    // 保存
    submit() {
      wx.showLoading({
        title: '保存中',
        mask: true
      })
      wx.cloud
        .callFunction({
          name: 'setCollege',
          data: {
            city: this.schoolForm.location,
            college: this.schoolForm.school,
            campus: this.schoolForm.area
          }
        })
        .then(res => {
          wx.setStorageSync('school', this.schoolForm.school + this.schoolForm.area)
          wx.hideLoading()
          wx.reLaunch({
            url: '/pages/school/school'
          })
        })
    }
  },
  onLoad() {
    this.getCollege()
  }
}
</script>

<style lang="scss">
.location {
  padding: 200rpx 100rpx;
  .submit {
    color: #fff;
    background-color: var(--themeColor);
  }
}
</style>
