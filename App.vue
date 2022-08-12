<template>
  <div id="app">
    <el-table :data="dataSource" :span-method="objectSpanMethod" style="width: 100%">
      <el-table-column prop="dwmc" label="单位">
        <template slot-scope="scope">
          {{ scope.row.dwmc }}
          <span @click="handleExpand(scope)">
            <i class="el-icon-arrow-right" v-if="scope.row.isExpand === false"></i>
            <i class="el-icon-arrow-down" v-if="scope.row.isExpand === true"></i>
          </span>
        </template>
      </el-table-column>
      <el-table-column prop="fxwts" label="问题数量"> </el-table-column>
      <el-table-column prop="questionType" label="问题类型"> </el-table-column>
      <el-table-column prop="questionTypeCount" label="问题个数"> </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {
    // HelloWorld
  },
  data() {
    return {
      dataSource: [
        {
          xh: 1,
          dwdm: '530100280000',
          dwmc: '成都高新区',
          bkhajs: 55,
          fxwts: 14,
          wtlx: {
            '没有户籍材料；扣2分': 7,
            '被拘留的女性违法行为人没有进行孕检；扣2分': 1,
            '无受案回执；扣2分': 6
          }
        },
        {
          xh: 1,
          dwdm: '530100280000',
          dwmc: '成都金牛区',
          bkhajs: 55,
          fxwts: 9,
          wtlx: {
            '没有前科资料；扣2分': 4,
            '没有正侧面照片；扣2分': 4,
            '案件空转或者绝大部分卷宗材料未上传；扣20分': 1
          }
        },
        {
          xh: 1,
          dwdm: '530100280000',
          dwmc: '成都锦江区',
          bkhajs: 55,
          fxwts: 0,
          // wtlx: {
          //   '传唤未告知家属；扣2分': 3,
          //   '无行政拘留执行回执；扣2分': 4,
          //   '行政处罚审批表审批层级错误，例如：拘留没有经过局领导审批的，只审批到派出所或者法制大队的；扣2分': 4
          // }
          wtlx: {}
        },
        {
          xh: 1,
          dwdm: '530100280000',
          dwmc: '成都青羊区',
          bkhajs: 89,
          fxwts: 0
          // wtlx: {
          //   '传唤未告知家属；扣2分': 3,
          //   '无行政拘留执行回执；扣2分': 4,
          //   '行政处罚审批表审批层级错误，例如：拘留没有经过局领导审批的，只审批到派出所或者法制大队的；扣2分': 4
          // }
          // wtlx: {}
        }
      ],
      copyDataSource: []
    }
  },
  methods: {
    handleExpand(scope) {
      const newData = []
      this.copyDataSource.forEach((item, i) => {
        const isExpand = scope.row.$idx === i ? (item.isExpand = !item.isExpand) : item.isExpand
        newData.push(
          {
            ...item,
            isExpand
          },
          ...(isExpand ? item.expandColumns.slice(1) : []) // slice去重第一项
        )
      })
      this.dataSource = newData
    },
    objectSpanMethod({ row, columnIndex }) {
      // console.log(row, columnIndex)
      if ([0, 1].includes(columnIndex)) {
        if (row.isExpand === true) {
          const len = row.expandColumns.length
          return {
            rowspan: len === 0 ? 1 : len,
            colspan: 1
          }
        } else if (row.isExpand === undefined) {
          return {
            rowspan: 0,
            colspan: 0
          }
        }
      }
    }
  },
  mounted() {
    this.dataSource = this.dataSource.map((item, i) => {
      // 存储初始化数据格式，方便后面操作
      const expandColumns = Object.entries(item.wtlx || {}).map(([key, value]) => {
        return { questionType: key, questionTypeCount: value }
      })
      return {
        ...item,
        questionType: expandColumns[0]?.questionType,
        questionTypeCount: expandColumns[0]?.questionTypeCount,
        expandColumns,
        isExpand: false, // 初始化默认箭头收起
        $idx: i // 为了记录带箭头的rowData是展开还是收起，从而影响是否展示隐藏的数据
      }
    })
    this.copyDataSource = [...this.dataSource]
  }
}
</script>

<style>
</style>
