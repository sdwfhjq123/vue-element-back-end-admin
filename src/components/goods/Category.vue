<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card>
      <el-row>
        <el-col>
          <el-button type="primary" @click="addCategoryClick">添加分类</el-button>
        </el-col>
      </el-row>

      <el-table
        :data="categoryList"
        row-key="cat_id"
        border
        :tree-props="{ children: 'children', hasChildren: 'children.length()' }"
      >
        <el-table-column type="index" label="#"></el-table-column>
        <el-table-column prop="cat_name" label="分类名称"></el-table-column>
        <el-table-column prop="cat_deleted" label="是否有效">
          <template slot-scope="scope">
            <i
              class="el-icon-success"
              v-if="scope.row.cat_deleted === false"
              style="color: lightgreen;"
            ></i>
            <i class="el-icon-error" v-else style="color: red;"></i>
          </template>
        </el-table-column>
        <el-table-column prop="cat_level" label="排序">
          <template slot-scope="scope">
            <el-tag size="mini" v-if="scope.row.cat_level === 0">一级</el-tag>
            <el-tag type="success" size="mini" v-else-if="scope.row.cat_level === 1">二级</el-tag>
            <el-tag type="warning" size="mini" v-else>三级</el-tag>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template>
            <el-button type="primary" icon="el-icon-edit" size="mini">编辑</el-button>
            <el-button type="danger" icon="el-icon-delete" size="mini">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[3, 5, 10, 15]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      ></el-pagination>
    </el-card>

    <el-dialog
      title="提示"
      :visible.sync="addCategoryDialogVisible"
      width="50%"
      @close="onAddCategoryDialogClosed"
    >
      <el-form :model="addCategoryForm" :rules="addCategoryFormRules" ref="addCategoryFormRef">
        <el-form-item label="活动名称" prop="cat_name" label-width="100px">
          <el-input v-model="addCategoryForm.cat_name"></el-input>
        </el-form-item>
        <el-form-item label="父级分类" label-width="100px">
          <el-cascader
            v-model="seletedCascaderValues"
            :options="parentCategoryList"
            :props="cascaderProps"
            @change="onParentCategoryChanged"
            clearable
          ></el-cascader>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addCategoryDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="onAddCategoryClick">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'Category',
  data () {
    return {
      queryInfo: {
        type: 3,
        pagenum: 1,
        pagesize: 5
      },
      total: 0,
      categoryList: [],
      addCategoryDialogVisible: false,
      addCategoryForm: {
        cat_name: '',
        cat_pid: 0,
        cat_level: 0
      },
      addCategoryFormRules: {
        cat_name: [{ required: true, message: '请输入分类名称', trigger: 'blur' }]
      },
      parentCategoryList: [],
      cascaderProps: {
        expandTrigger: 'hover',
        value: 'cat_id',
        label: 'cat_name',
        children: 'children',
        checkStrictly: true
      },
      seletedCascaderValues: []
    }
  },
  created () {
    this.getCateList()
  },
  methods: {
    async getCateList () {
      const { data: res } = await this.$http.get('categories', {
        params: this.queryInfo
      })

      if (res.meta.status !== 200) {
        return this.$message.error('获取商品列表失败')
      }

      this.categoryList = res.data.result
      this.total = res.data.total
    },
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getCateList()
    },
    // 监听页码值改变的事件
    handleCurrentChange (newPage) {
      this.queryInfo.pagenum = newPage
      this.getCateList()
    },
    addCategoryClick () {
      this.getParentCateList()
      this.addCategoryDialogVisible = true
    },
    async getParentCateList () {
      const { data: res } = await this.$http.get('categories', { params: { type: 2 } })

      if (res.meta.status !== 200) {
        return this.$message.error('获取父级分类数据失败')
      }

      this.parentCategoryList = res.data
    },
    onParentCategoryChanged () {
      if (this.seletedCascaderValues.length > 0) {
        this.addCategoryForm.cat_level = this.seletedCascaderValues.length
        this.addCategoryForm.cat_pid = this.seletedCascaderValues[
          this.seletedCascaderValues.length - 1
        ]
      } else {
        this.addCategoryForm.cat_level = 0
        this.addCategoryForm.cat_pid = 0
      }
    },
    onAddCategoryDialogClosed () {
      this.$refs.addCategoryFormRef.resetFields()
      this.seletedCascaderValues = []
      this.addCategoryForm.cat_pid = ''
      this.addCategoryForm.cat_level = ''
    },
    async onAddCategoryClick () {
      this.$refs.addCategoryFormRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.post('categories', this.addCategoryForm)

        console.log(res)
        console.log(this.addCateForm)

        if (res.meta.status !== 201) {
          return this.$message.error('添加分类失败！')
        }

        this.$message.success('添加分类成功！')
        this.getCateList()
        this.addCategoryDialogVisible = false
      })
    }
  }
}
</script>

<style lang="less" scoped></style>
