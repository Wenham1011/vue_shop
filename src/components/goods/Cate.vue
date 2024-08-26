<script>
export default {
  data() {
    return {
      addCateDialogVisible: false,
      editDialogVisible: false,
      // 查询条件
      queryInfo: {
        type: 3,
        pagenum: 1,
        pagesize: 5
      },
      // 为table指定列
      columns: [
        {
          label: '分类名',
          prop: 'cat_name'
        },
        {
          label: '是否有效',
          // 表示使用自定义模版列
          type: 'template',
          // 表示当前这一列使用的模版名称
          template: 'isOK'
        },
        {
          label: '排序',
          // 表示使用自定义模版列
          type: 'template',
          // 表示当前这一列使用的模版名称
          template: 'order'
        },
        {
          label: '操作',
          // 表示使用自定义模版列
          type: 'template',
          // 表示当前这一列使用的模版名称
          template: 'opt'
        }
      ],
      // 总数据条数
      total: 0,
      // 商品分类的数据列表
      catelist: [],
      // 添加分类表单的数据对象
      addCateForm: {
        cat_name: '',
        cat_pid: 0,
        cat_level: 0
      },
      // 添加分类表单的验证规则对象
      addCateFormRules: {
        cat_name: [
          { required: true, message: '请输入分类名称', trigger: 'blur' }
        ]
      },
      editFormRules: {
        email: [
          { required: true, message: '请输入分类名', trigger: 'blur' }
        ]
      },
      // 根据id查询到的数据|编辑用户的表单数据
      editForm: {},
      // 父级分类列表
      parentCateList: [],
      // cascader配置对象
      cascaderProps: {
        expandTrigger: 'hover',
        checkStrictly: true,
        value: 'cat_id',
        label: 'cat_name',
        children: 'children'
      },
      // 选中的父级分类的id数组
      selectedKeys: []
    }
  },
  created() {
    this.getCateList()
  },
  methods: {
    async getCateList() {
      const { data: res } = await this.$http.get('categories', { params: this.queryInfo })
      if (res.meta.status !== 200) {
        return this.$message.error('获取商品分类失败')
      }
      // console.log(res.data)
      this.catelist = res.data.result
      this.total = res.data.total
    },
    // 监听pagesize改变
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize
      this.getCateList()
    },
    // 监听pagenum的改变
    handleCurrentChange(newPage) {
      this.queryInfo.pagenum = newPage
      this.getCateList()
    },
    // 点击“添加分类”按钮的监听事件
    showAddCateDialog() {
      this.getParentCateList()
      this.addCateDialogVisible = true
    },
    async getParentCateList() {
      const { data: res } = await this.$http.get('categories', { params: { type: 2 } })
      if (res.meta.status !== 200) {
        return this.$message.error('获取父级分类数据失败')
      }
      console.log(res.data)
      this.parentCateList = res.data
    },
    // 选择项发生变化，触发
    parentCateChange() {
      console.log(this.selectedKeys)
      if (this.selectedKeys.length > 0) {
        this.addCateForm.cat_pid = this.selectedKeys[this.selectedKeys.length - 1]
        this.addCateForm.cat_level = this.selectedKeys.length
      }
      else {
        this.addCateForm.cat_pid = 0
        this.addCateForm.cat_level = 0
      }
    },
    // 关闭时重置表单
    addCateDialogClosed() {
      this.$refs.addCateFormRef.resetFields()
      this.selectedKeys = []
      this.addCateForm.cat_level = 0
      this.addCateForm.cat_pid = 0
    },
    editDialogClosed() {
      this.$refs.editFormRef.resetFields()
    },
    // 表单按钮‘添加’的监听事件
    addCate() {
      this.$refs.addCateFormRef.validate(async valid => {
        if (!valid) return
        const {data: res} = await this.$http.post('categories', this.addCateForm)

        if (res.meta.status !== 201) {
          return this.$message.error('添加分类失败')
        }

        this.$message.success('添加分类成功')
        this.getCateList()
        this.addCateDialogVisible = false
      })
    },
    // 监听“操作”中的编辑按钮
    async showEditDialog(id) {
      console.log(id)
      const { data: res } = await this.$http.get('categories/' + id)
      // console.log(res.meta.status)
      console.log(res)
      if (res.meta.status !== 200) return this.$message.error('请求失败,无法查询信息')
      this.editForm = res.data
      this.editDialogVisible = true
    },
    editCate() {
      this.$refs.editFormRef.validate(async valid => {
        // console.log(valid)
        if (!valid) return this.$message.error('格式错误，修改分类失败')
        // 发起修改用户请求
        const { data: res } = await this.$http.put('categories/' + this.editForm.cat_id, {cat_name: this.editForm.cat_name})
        if (res.meta.status !== 200) {
          return this.$message.error('请求失败，无法修改分类')
        }
        this.editDialogVisible = false
        this.getCateList()
        this.$message.success('修改分类成功')
      })
    },
    async removeCateById(id) {
      // 弹出提示框
      const confirmResult = await this.$confirm('此操作将永久删除该类别, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err => err)
      // 如果用户点击‘确定’，则返回一个字符串confrim；点击‘取消’，则返回字符串cancel
      // console.log(confirmResult)
      if (confirmResult !== 'confirm') {
        return this.$message.info('已取消删除')
      }
      const { data: res } = await this.$http.delete('categories' + id)
      if (res.meta.status !== 200) {
        return this.$message.error('请求失败，无法删除')
      }
      this.$message.success('删除成功')
      this.getCateList()
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
      <el-breadcrumb-item>商品分类</el-breadcrumb-item>
    </el-breadcrumb>

    <!--卡片视图-->
    <el-card>
      <!--添加商品类别按钮-->
      <el-row>
        <el-col>
          <el-button type="primary" @click="showAddCateDialog">添加分类</el-button>
        </el-col>
      </el-row>
      <!--商品分类的表格区域-->
      <tree-table class="tree-table" :data="catelist" :columns="columns" :selection-type="false" :expand-type="false"
                  show-index
                  index-text="#" border>
        <!--是否有效模版-->
        <template slot="isOK" slot-scope="scope">
          <i class="el-icon-success" v-if="scope.row.cat_deleted === false" style="color: lightgreen"></i>
          <i class="el-icon-error" v-else style="color: lightgreen"></i>
        </template>
        <!--排序模版-->
        <template slot="order" slot-scope="scope">
          <el-tag size="mini" v-if="scope.row.cat_level===0">一级</el-tag>
          <el-tag size="mini" type="success" v-else-if="scope.row.cat_level===1">二级</el-tag>
          <el-tag size="mini" type="warning" v-else>三级</el-tag>
        </template>
        <!--操作模版-->
        <template slot="opt" slot-scope="scope">
          <el-button type="primary" size="mini" icon="el-icon-edit" @click="showEditDialog(scope.row.cat_id)">编辑</el-button>
          <el-button type="danger" size="mini" icon="el-icon-delete" @click="removeCateById(scope.row.cat_id)">删除</el-button>
        </template>
      </tree-table>

      <!--分页区域-->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[3, 5, 10]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total">
      </el-pagination>
    </el-card>

    <!--添加分类的对话框-->
    <el-dialog
      title="添加分类"
      :visible.sync="addCateDialogVisible"
      width="50%"
      @close="addCateDialogClosed">
      <!--添加分类的表单-->
      <el-form :rules="addCateFormRules" ref="addCateFormRef" :model="addCateForm" label-width="100px">
        <el-form-item label="分类名称" prop="cat_name">
          <el-input v-model="addCateForm.cat_name"></el-input>
        </el-form-item>
        <el-form-item label="父级分类">
          <el-cascader
            v-model="selectedKeys"
            :options="parentCateList"
            :props="cascaderProps"
            :clearable="true"
            @change="parentCateChange"></el-cascader>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
    <el-button @click="addCateDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="addCate">确 定</el-button>
  </span>
    </el-dialog>
    <!--修改用户的对话框-->
    <el-dialog title="修改分类" :visible.sync="editDialogVisible" width="50%" @close="editDialogClosed">
      <el-form :model="editForm" :rules="editFormRules" ref="editFormRef" label-width="70px">
        <el-form-item label="商品分类">
          <el-input v-model="editForm.cat_name"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
    <el-button @click="editDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="editCate">确 定</el-button>
  </span>
    </el-dialog>
  </div>
</template>

<style scoped lang="less">
.tree-table {
  margin-top: 10px;
}

.el-cascader {
  width: 100%;
  max-height: 300px;
}

</style>
