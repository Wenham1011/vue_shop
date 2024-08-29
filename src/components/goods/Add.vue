<script>
import _ from 'lodash'
export default {
  data() {
    return {
      activeIndex: '0',
      addForm: {
        goods_name: '',
        goods_price: 0,
        goods_weight: 0,
        goods_number: 0,
        // 商品所属的分类数组
        goods_cat: [],
        // 图片的数组
        pics: [],
        // 商品的详情描述
        goods_introduce: ''
      },
      addFormRules: {
        goods_name: [
          { required: true, message: '请输入商品名称', trigger: 'blur' }
        ],
        goods_price: [
          { required: true, message: '请输入商品价格', trigger: 'blur' }
        ],
        goods_weight: [
          { required: true, message: '请输入商品重量', trigger: 'blur' }
        ],
        goods_number: [
          { required: true, message: '请输入商品数量', trigger: 'blur' }
        ],
        goods_cat: [
          { required: true, message: '请输入商品分类', trigger: 'blur' }
        ]
      },
      addFormRef: {},
      catelist: [],
      cateProps: {
        label: 'cat_name',
        value: 'cat_id',
        children: 'children'
      },
      headersObj: {
        Authorization: window.sessionStorage.getItem('token')
      },
      previewPath: '',
      previewVisible: false
    }
  },
  created() {
    this.getCateList()
  },
  methods: {
    async getCateList() {
      const { data: res } = await this.$http.get('categories')
      if (res.meta.status !== 200) {
        return this.$message.error('获取商品分类失败')
      }
      // console.log(res.data)
      this.catelist = res.data
    },
    handleChange() {
      console.log(this.addForm.goods_cat)
      if (this.addForm.goods_cat.length !== 3) {
        this.addForm.goods_cat = []
      }
    },
    beforeTabLeave(activeName, oldActiveName) {
      if (oldActiveName === '0' && this.addForm.goods_cat.length !== 3) {
        this.$message.error('请先选择商品分类')
        // 开发完成后，解注释下面一行******************************************************
        // return false
      }
    },
    handlePreview(file) {
      // console.log(file)
      this.previewPath = file.response.data.url
      this.previewVisible = true
    },
    handleRemove(file) {
      // 1. 获取想要删除图片的临时路径
      const filePath = file.response.data.tmp_path
      // 2.从pics数组中，找到这个图片对应的索引值
      const i = this.addForm.pics.findIndex(x => x.pic === filePath)
      // 3.调用数组的splice方法，把图片信息对象，从pics数组中移除
      this.addForm.pics.splice(i, 1)
      // console.log(this.addForm)
    },
    // 监听图片上传成功事件
    handleSuccess(response) {
      // console.log(response)
      // 1.拼接得到图片信息对象
      // 2.将图片信息对象添加到pics数组中
      const picInfo = { pic: response.data.tmp_path }
      this.addForm.pics.push(picInfo)
    },

    // 最终：添加商品
    add() {
      this.$refs.addFormRef.validate(async valid => {
        if (!valid) {
          return this.$message.error('请填写必要表单项')
        }

        // 处理添加的业务逻辑
        // lodash cloneDeep(obj) 拷贝一份
        const form = _.cloneDeep(this.addForm)
        form.goods_cat = form.goods_cat.join(',')

        // 正式发起请求
        const {data: res} = await this.$http.post('goods',form)
        if (res.meta.status !== 201) {
          this.$message.error('添加商品失败')
        }
        this.$message.success('添加商品成功')
        this.$router.push('/goods')
      })
    }
  }
}

</script>

<template>
  <div>
    <!--面包屑导航区-->
    <el-breadcrumb>
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>添加商品</el-breadcrumb-item>
    </el-breadcrumb>

    <!--  卡片视图-->
    <el-card>
      <!--提示区-->
      <el-alert
        title="添加商品信息"
        :closable="false"
        show-icon
        type="info">
      </el-alert>
      <!--进度条-->
      <el-steps :space="200" :active="activeIndex - 0" finish-status="success">
        <el-step title="基本信息"></el-step>
        <!--        <el-step title="商品参数"></el-step>-->
        <!--        <el-step title="商品属性"></el-step>-->
        <el-step title="商品图片"></el-step>
        <el-step title="商品内容"></el-step>
        <el-step title="完成"></el-step>
      </el-steps>

      <!--tab区-->
      <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="100px" label-position="top">
        <el-tabs v-model="activeIndex" tab-position="left" :before-leave="beforeTabLeave">
          <el-tab-pane label="基本信息" name="0">
            <el-form-item label="商品名称" prop="goods_name">
              <el-input v-model="addForm.goods_name"></el-input>
            </el-form-item>
            <el-form-item label="商品价格" prop="goods_price">
              <el-input v-model="addForm.goods_price" type="number"></el-input>
            </el-form-item>
            <el-form-item label="商品重量" prop="goods_weight">
              <el-input v-model="addForm.goods_weight"></el-input>
            </el-form-item>
            <el-form-item label="商品数量" prop="goods_number">
              <el-input v-model="addForm.goods_number"></el-input>
            </el-form-item>
            <el-form-item label="商品分类" prop="">
              <el-cascader
                v-model="addForm.goods_cat"
                :options="catelist"
                :props="cateProps"
                :clearable="true"
                @change="handleChange"></el-cascader>
            </el-form-item>
          </el-tab-pane>
          <!--          <el-tab-pane label="商品参数" name="1"></el-tab-pane>-->
          <!--          <el-tab-pane label="商品属性" name="1"></el-tab-pane>-->
          <el-tab-pane label="商品图片" name="1">
            <!-- action 表示图片上传到后台的API地址-->
            <el-upload
              action="http://127.0.0.1:8888/api/private/v1/upload"
              :on-preview="handlePreview"
              :on-remove="handleRemove"
              :headers="headersObj"
              :on-success="handleSuccess"
              list-type="picture">
              <el-button size="small" type="primary">点击上传</el-button>
            </el-upload>
          </el-tab-pane>
          <el-tab-pane label="商品内容" name="2">
            <!--富文本编辑器-->
            <quill-editor v-model="addForm.goods_introduce"/>
            <el-button type="primary" style="margin-top: 15px">添加商品</el-button>
          </el-tab-pane>
        </el-tabs>
      </el-form>
    </el-card>

    <!--预览图片的弹框-->
    <el-dialog
      title="图片预览"
      :visible.sync="previewVisible"
      width="50%">
      <img :src="previewPath" alt="预览的图片" class="previewImg">
    </el-dialog>
  </div>
</template>

<style scoped lang="less">
.el-steps {
  margin: 15px 0;
}

.el-steps_title {
  font-size: 13px;
}

.previewImg {
  width: 100%;
}
</style>
