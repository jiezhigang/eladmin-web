<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">订单编号</label>
        <el-input v-model="query.id" clearable placeholder="订单编号" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">平台编号</label>
        <el-input v-model="query.orderId" clearable placeholder="平台编号" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">订单已被读取</label>
        <el-input v-model="query.orderRead" clearable placeholder="订单已被读取" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <date-range-picker
          v-model="query.createTime"
          start-placeholder="createTimeStart"
          end-placeholder="createTimeStart"
          class="date-item"
        />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="订单编号" prop="id">
            <el-input v-model="form.id" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="平台编号" prop="orderId">
            <el-input v-model="form.orderId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单已被读取">
                未设置字典，请手动设置 Radio
          </el-form-item>
          <el-form-item label="配货完成状态">
                未设置字典，请手动设置 Radio
          </el-form-item>
          <el-form-item label="订单离港状态">
                未设置字典，请手动设置 Radio
          </el-form-item>
          <el-form-item label="类型">
            <el-input v-model="form.type" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="派件">
                未设置字典，请手动设置 Radio
          </el-form-item>
          <el-form-item label="已经签收">
                未设置字典，请手动设置 Radio
          </el-form-item>
          <el-form-item label="创建时间">
            <el-date-picker v-model="form.createTime" type="datetime" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.status.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
        <el-table-column prop="id" label="订单编号" />
        <el-table-column prop="orderId" label="平台编号" />
        <el-table-column prop="orderRead" label="订单已被读取" />
        <el-table-column prop="orderAllocate" label="配货完成状态" />
        <el-table-column prop="orderOffHarbour" label="订单离港状态" />
        <el-table-column prop="type" label="类型" />
        <el-table-column prop="isDelivery" label="派件" />
        <el-table-column prop="isSignIn" label="已经签收" />
        <el-table-column prop="createTime" label="创建时间" />
        <el-table-column v-if="checkPer(['admin','tradeOrderRelationship:edit','tradeOrderRelationship:del'])" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudTradeOrderRelationship from '@/api/tradeOrderRelationship'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, orderId: null, orderRead: null, orderAllocate: null, orderOffHarbour: null, type: null, isDelivery: null, isSignIn: null, createTime: null }
export default {
  name: 'TradeOrderRelationship',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '惊天一城', url: 'api/tradeOrderRelationship', idField: 'id', sort: 'id,desc', crudMethod: { ...crudTradeOrderRelationship }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'tradeOrderRelationship:add'],
        edit: ['admin', 'tradeOrderRelationship:edit'],
        del: ['admin', 'tradeOrderRelationship:del']
      },
      rules: {
        id: [
          { required: true, message: '订单编号不能为空', trigger: 'blur' }
        ],
        orderId: [
          { required: true, message: '平台编号不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'id', display_name: '订单编号' },
        { key: 'orderId', display_name: '平台编号' },
        { key: 'orderRead', display_name: '订单已被读取' }
      ]
    }
  },
  methods: {
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>

<style scoped>

</style>
