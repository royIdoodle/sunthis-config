<template>
  <el-form ref="form" :model="form" label-width="200px">
    <el-form-item label="按钮类型">
      <el-radio-group v-model="form.style">
        <el-radio label="inline">行内</el-radio>
        <el-radio label="sticky">固定</el-radio>
      </el-radio-group>
    </el-form-item>

    <el-form-item label="按钮位置" v-if="form.style === 'inline'">
      <el-radio-group v-model="form.alignmentInline">
        <el-radio label="center">居中</el-radio>
        <el-radio label="left">居左</el-radio>
        <el-radio label="right">居右</el-radio>
      </el-radio-group>
    </el-form-item>

    <el-form-item label="按钮位置" v-if="form.style === 'sticky'">
      <el-radio-group v-model="form.alignmentSticky">
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
          alignmentInline: 'center',
          alignmentSticky: 'sticky',
          title: '',
          description: '',
          networks: ['wechat', 'weibo','facebook','twitter'],
          url: ''
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
        return {settings: JSON.stringify({
            style: this.form.style,
            image: this.form.image,
            alignment: this.form.style === 'inline'? this.form.alignmentInline:this.form.alignmentSticky,
            title: this.form.title,
            description: this.form.description,
            networks: this.form.networks,
            url: this.form.url
          })}
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