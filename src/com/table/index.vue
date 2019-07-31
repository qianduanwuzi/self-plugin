<template>
  <div class="self_table_container">
    <!-- header ope -->
    <div class="header_ope_box flex_align">
      <div class="left flex_align">
        <div class="vertical_box"></div>
        <div class="table_name">{{`${txt}(${pageTotal})`}}</div>
      </div>
      <div class="right flex_align">
        <slot name="ope"></slot>
      </div>
    </div>
    <el-table
      v-loading="tableLoading"
      element-loading-text="加载中"
      stripe
      element-loading-spinner="el-icon-loading"
      :data="tableData"
      style="width: 100%"
    >
      <template v-for="one in columnConfig">
        <!-- extra operator -->
        <el-table-column :label="one.label" v-if="one.slot">
          <template slot-scope="scope">
            <slot :rowdata="Object.assign({key: one.key}, scope.row)"></slot>
          </template>
        </el-table-column>
        <!-- normal -->
        <el-table-column :prop="one.key" :label="one.label" v-else-if="!one.adapter" ></el-table-column>
        <!-- special handle with a method -->
        <el-table-column :label="one.label" v-else>
          <template slot-scope="scope">{{one.adapter(scope.row[one.key])}}</template>
        </el-table-column>
      </template>
      <template slot="empty">
          <img src="@/assets/table_no_data.png" alt="" style="margin: 116px auto 0 auto">
      </template>  
    </el-table>
    <el-pagination
      class="table_page"
      v-if="pageTotal"
      @current-change="handleCurrentChange"
      :current-page.sync="currentPage"
      :page-size="pageSize"
      layout="prev, pager, next, jumper"
      :total="pageTotal"
    ></el-pagination>
  </div>
</template>

<script>
import axios from 'axios'
import config from '@/config.js';
export default {
  props: {
    reload: {
      // 重新加载数据
      default: false,
      type: Boolean
    },
    columnConfig: {
      // 列配置
      default: () => [],
      type: Array
    },
    pageSize: {
      default: 20,
      type: Number
    },
    api: String, // 表格数据接口
    params: Object, // search参数
    txt: String,
    userId: {
      default: false,
      type: Boolean
    }
  },
  data() {
    return {
      tableLoading: false,
      tableData: [],
      currentPage: 1,
      pageTotal: 0
    };
  },
  mounted() {
    // this.getData()
  },
  methods: {
     loadPage() {
      this.loading = this.$loading({
        lock: true,
        text: "Loading",
        spinner: "el-icon-loading",
        background: "rgba(0, 0, 0, 0.7)"
      });
    },
    handleCurrentChange(v) {
      this.getData(v)
    },
    getData(page = 1) {
      if(this.userId) {
        // this.params.user_id = 100000 
        this.params.user_id = JSON.parse(localStorage.getItem('userInfo')).user_id
      }
      this.loadPage()
      axios.post(window.$$domain_setting.api+this.api, {page: page, pagesize: 20, ...this.params} , { headers: config.header() }).then(res => {
        // console.log('tabledata', res.data.data.data)
        this.loading.close()
        this.tableData = res.data.data.data
        this.pageTotal = res.data.data.total
      })
    }
  },
  watch: {
    reload() {
      this.getData();
    }
  }
};
</script>

<style lang="scss" scoped>
.self_table_container {
  .header_ope_box {
    margin-top: 24px;
    margin-bottom: 12px;
    justify-content: space-between;
    .left {
      .vertical_box {
        width: 4px;
        height: 16px;
        background: $theme_color;
        border-radius: 2px;
      }
      .table_name {
        // width: 110px;
        // height: 25px;
        margin-left: 8px;
        font-size: 18px;
        font-family: PingFangSC-Semibold;
        font-weight: 600;
        color: rgba(51, 51, 51, 1);
        // line-height: 25px;
      }
    }
    .right {
      // float: right;
    }
  }
  /deep/ .el-table th, /deep/ .el-table tr{
    height: 53px;
  }
  .table_page {
    padding: 24px;
    text-align: right;
    padding-bottom: 10px;
    padding-right: 22px;
  }
}
</style>


