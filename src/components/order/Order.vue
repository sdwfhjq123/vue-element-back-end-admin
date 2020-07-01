<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>订单管理</el-breadcrumb-item>
      <el-breadcrumb-item>订单列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card>
      <el-row>
        <el-col :span="8">
          <el-input placeholder="请输入内容">
            <el-button slot="append" icon="el-icon-search"></el-button>
          </el-input>
        </el-col>
      </el-row>

      <el-table :data="orderList" stripe border max-height="650px">
        <el-table-column type="index" label="#"></el-table-column>
        <el-table-column prop="order_number" label="订单编号"></el-table-column>
        <el-table-column
          prop="order_price"
          label="订单价格"
          width="100px"
        ></el-table-column>
        <el-table-column
          prop="is_send"
          label="是否发货"
          width="100px"
        ></el-table-column>
        <el-table-column prop="pay_status" label="是否付款" width="100px">
          <template slot-scope="scope">
            <el-tag type="success" v-if="scope.row.pay_status === '1'">
              已付款
            </el-tag>
            <el-tag type="danger" v-else>未付款</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="create_time" label="下单时间" width="170px">
          <template slot-scope="scope">
            {{ scope.row.create_time | dateFormat }}
          </template>
        </el-table-column>
        <el-table-column label="操作" width="140px">
          <template>
            <el-button type="primary" icon="el-icon-edit" size="mini">
            </el-button>
            <el-button type="success" icon="el-icon-location" size="mini">
            </el-button>
          </template>
        </el-table-column>
      </el-table>

      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[5, 10, 15]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      ></el-pagination>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'Order',
  data () {
    return {
      queryInfo: {
        query: '',
        pagesize: 10,
        pagenum: 1
      },
      total: 0,
      orderList: []
    }
  },
  created () {
    this.getOrderList()
  },
  methods: {
    async getOrderList () {
      const { data: res } = await this.$http('orders', {
        params: this.queryInfo
      })

      if (res.meta.status !== 200) {
        return this.$message.error('获取订单列表失败')
      }

      this.total = res.data.total
      this.orderList = res.data.goods

      console.log(this.orderList)
    },
    handleSizeChange (newPageSize) {
      this.queryInfo.pagesize = newPageSize
      this.getOrderList()
    },
    handleCurrentChange (newPageNum) {
      this.queryInfo.pagenum = newPageNum
      this.getOrderList()
    }
  }
}
</script>

<style lang="less" scoped>
.el-table {
  font-size: 13px;
}
</style>
