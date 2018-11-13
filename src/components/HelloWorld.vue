<template>
  <el-form ref="form" :model="form" label-width="200px">
    <el-form-item label="按钮类型">
      <el-radio-group v-model="form.style">
        <el-radio label="inline">行内</el-radio>
        <el-radio label="sticky">固定</el-radio>
      </el-radio-group>
    </el-form-item>

    <el-form-item label="按钮位置">
      <el-radio-group v-model="form.alignment">
        <el-radio v-if="form.style === 'inline'" label="center">居中</el-radio>
        <el-radio label="left">居左</el-radio>
        <el-radio label="right">居右</el-radio>
      </el-radio-group>
    </el-form-item>


    <el-form-item label="分享标题">
      <el-input v-model="form.title"></el-input>
    </el-form-item>

    <el-form-item label="分享描述">
      <el-input v-model="form.description"></el-input>
    </el-form-item>

    <el-form-item label="分享链接">
      <el-input v-model="form.url"></el-input>
    </el-form-item>

    <el-form-item label="分享图片（外部url）">
      <el-input v-model="form.image"></el-input>
    </el-form-item>

    <el-form-item label="分享渠道">
      <el-checkbox-group v-model="form.networks">
        <el-checkbox v-for="(item, idx) in networks" :key="idx"
                     :label="item" name="type"></el-checkbox>
      </el-checkbox-group>
    </el-form-item>


    <!-- 新追加 -->
    <el-form-item label="语言 language">
      <el-radio-group v-model="form.language">
        <el-radio label="en">英文</el-radio>
        <el-radio label="zh">中文</el-radio>
      </el-radio-group>
    </el-form-item>

    <el-form-item label="按钮显示 labels">
      <el-radio-group v-model="form.labels">
        <el-radio label="cta">普通</el-radio>
        <el-radio label="counts">分享计数器</el-radio>
      </el-radio-group>
    </el-form-item>

    <el-form-item label="内边距 padding">
      <el-input type="number" v-model="form.padding"></el-input>
    </el-form-item>

    <el-form-item label="圆角 radius">
      <el-input type="number" v-model="form.radius"></el-input>
    </el-form-item>

    <el-form-item label="按钮隐藏 enabled">
      <el-switch v-model="form.enabled"></el-switch>
    </el-form-item>

    <el-form-item label="字号 radius">
      <el-input type="number" v-model="form.fontSize"></el-input>
    </el-form-item>

    <el-form-item label="显示总数 showTotal">
      <el-switch v-model="form.showTotal"></el-switch>
    </el-form-item>

    <el-form-item label="尺寸 size">
      <el-input type="number" v-model="form.size"></el-input>
    </el-form-item>

    <el-form-item label="顶部位置 top">
      <el-input type="number" v-model="form.top"></el-input>
    </el-form-item>
    <el-form-item label="桌面下隐藏 hideDesktop">
      <el-switch v-model="form.hideDesktop"></el-switch>
    </el-form-item>

    <el-form-item label="最小数量 minCount">
      <el-input type="number" v-model="form.minCount"></el-input>
    </el-form-item>

    <el-form-item label="移动端显示 showMobile">
      <el-switch v-model="form.showMobile"></el-switch>
    </el-form-item>

    <el-form-item label="显示切换按钮 showToggle">
      <el-switch v-model="form.showToggle"></el-switch>
    </el-form-item>



    <!---->
    <el-form-item label="当前id">
      <el-input type="text" readonly disabled v-model="id"/>
    </el-form-item>

    <el-form-item>
      <el-button v-if="id" type="warning" @click="update">修改</el-button>
      <el-button v-if="!id" type="primary" @click="make">立即创建</el-button>
    </el-form-item>
  </el-form>

</template>
<script>
  // const urlPrefix = 'http://localhost:3000'
  const urlPrefix ='https://mmd.wangchong.tech'
  const networks = ['wechat', 'weibo','facebook','twitter',
    'blogger', 'delicious', 'digg', 'email',  'flipboard', 'google', 'linkedin',
    'livejournal', 'mailru', 'meneame', 'messenger', 'oknoklassniki', 'pinterest', 'print',
    'reddit', 'sharethis', 'sms', 'stumbleupon', 'tumblr',   'vk',
    'whatsapp', 'xing'
  ]
import axios from 'axios'
  //'wechat', 'linkedin', 'messenger', 'twitter'
  export default {
    data() {
      return {
        id: '',
        networks: networks,
        form: {
          style: 'inline',
          image: '',
          alignment: 'left',
          alignmentSticky: 'sticky',
          title: '',
          description: '',
          networks: ['wechat', 'weibo','facebook','twitter'],
          url: '',


          language: 'zh',
          labels: 'cta',
          padding: 12,
          radius: 4,
          enabled: true,
          fontSize: 16,
          showTotal: false,
          size: 40,
          hideDesktop: false,
          minCount: 0,
          showMobile: true,
          showToggle: true,
          top: 160
        }
      }
    },
    mounted () {
      this.id = this.$route.query.id
      this.id && axios.get(`${urlPrefix}/share/get/${this.id}`).then(({data}) => {
        if (data.data.settings) {
          this.form = JSON.parse(data.data.settings)
        }
      }).catch(console.error)
    },
    methods: {
      resolveData () {
        return {settings: JSON.stringify(this.form)}
      },
      make () {
        const data = this.resolveData()
        console.log(data)
        axios({
          method: 'post',
          responseType: 'json',
          url: `${urlPrefix}/share/add`,
          data
        }).then(({data}) => {
          this.$route.query.id = data.data._id
          this.id = data.data._id
          this.$router.replace({ query: { id: this.id }})
        })
      },
      update () {
        const data = this.resolveData()
        console.log(data)
        axios({
          method: 'post',
          responseType: 'json',
          url: `${urlPrefix}/share/update/${this.id}`,
          data
        }).then(({data}) => {

        })
      }
    }
  }
</script>