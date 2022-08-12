<!--
@author ch
@date 2022/8/1
@describe 专题考评统计分析
-->
<template>
  <div class="zfjdtjfx">
    <!--  整体概括-->
    <div class="content-line-3 marginTop2">
      <box-name box-name="总体概括"></box-name>
      <div class="HTflex-bc marginTop2">
        <div class="marginRight2  topBox  HTflex-bc">
          <div class="icon">
            <img src="../../../assets/images/icon/fly.png" />
          </div>
          <div class="titleNum">
            <div class="title">考核考评次数/次</div>
            <span class="Num">{{ gkInfo.khkpcs }}</span>
          </div>
        </div>
        <div class="marginRight2 topBox HTflex-bc">
          <div class="icon">
            <img src="../../../assets/images/icon/fly.png" />
          </div>
          <div class="titleNum">
            <div class="title">涉及案件数 /件</div>
            <span class="Num">{{ gkInfo.sjajs }}</span>
          </div>
          <div class="type">
            <div class="caseBox marginBottom2 HTflex-bc">
              <span>刑事案件数 /件</span>
              <span>{{ gkInfo.xsajs }}</span>
            </div>
            <div class="caseBox HTflex-bc">
              <span class="red">行政案件数 /件</span>
              <span>{{ gkInfo.xzajs }}</span>
            </div>
          </div>
        </div>
        <div class="marginRight2  topBox  HTflex-bc">
          <div class="icon">
            <img src="../../../assets/images/icon/fly.png" />
          </div>
          <div class="titleNum">
            <div class="title">发现问题数 /件</div>
            <span class="Num">{{ gkInfo.fxwts }}</span>
          </div>
        </div>
        <div class=" topBox  HTflex-bc">
          <div class="icon">
            <img src="../../../assets/images/icon/fly.png" />
          </div>
          <div class="titleNum">
            <div class="title">涉及考核单位 /个</div>
            <span class="Num">{{ gkInfo.sjkhdw }}</span>
          </div>
        </div>
      </div>
    </div>
    <!--  案件类型考评占比情况-->
    <div class="HTflex-b marginTop2">
      <div class="content-line-3 flex-1 marginTop2">
        <box-name box-name="案件类型考评占比情况"></box-name>
        <div
          class="box-normal marginTop2  marginRight2 HTflex-ac"
          style="position: relative;"
        >
          <div class="canvas-area">
            <pie :jdc-opt="caseTypeCanvas"></pie>
          </div>
          <div class="canvas-info-area ">
            <div class="marginBottom2">
              <div class="canvas-info-area-name normal-color">刑事案件</div>
              <div>
                <span class="click-notTable-num">{{ gkInfo.xsajs }}</span>
                <span class="canvas-info-area-dw">/件</span>
              </div>
            </div>
            <div class>
              <div class="canvas-info-area-name abnormal-color">行政案件</div>
              <div>
                <span class="click-notTable-num">{{ gkInfo.xzajs }}</span>
                <span class="canvas-info-area-dw">/件</span>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="content-line-3 flex-1 marginTop2">
        <box-name box-name="问题类型排名分析"></box-name>
        <div class="box-normal marginTop2 flex-1">
          <div class="tip HTflex-b marginTop2">
            <div>注：按出现次数由高到低排列</div>
          </div>
          <div class="marginTop2" style="overflow: hidden;height:100%;">
            <info-table
              :default-sort="{ prop: 'cs', order: 'descending' }"
              style="margin:0 0.9375rem"
              class="info-table"
              v-bind="WtLxTable"
            ></info-table>
          </div>
        </div>
      </div>
    </div>
    <!--  被考核单位情况分析-->
    <div class="content-line-3 marginTop2">
      <div class="HTflex-b marginTop2">
        <box-name box-name="被考核单位情况分析"></box-name>
        <down-excel name="导出excel" @downExcel="getTbaleTjData()"></down-excel>
      </div>

      <el-card class="marginTop2 zsTable FczTable">
        <box-tabs
          :active.sync="tableTabs.active"
          :tab-arr="tableTabs.arr"
        ></box-tabs>
        <div style="padding:1rem;height:35rem">
          <div
            class="marginBottom2"
            v-if="tableTabs.active == 2 || tableTabs.active == 4"
          >
            <el-radio v-model="radio" label="1">仅看本单位直属人员</el-radio>
            <el-radio v-model="radio" label="2"
              >全部人员（包含下属单位人员）</el-radio
            >
          </div>
          <el-table
            :data="tableData.rows"
            :span-method="objectSpanMethod"
            style="width: 100%"
            :height="
              tableTabs.active == 2 || tableTabs.active == 4 ? '85%' : '90%'
            "
          >
            <template slot="empty">
              <img
                src="../../../assets/images/conNull2.png"
                alt
                style="margin: 120px auto 8px auto; display: block"
              />
              <p>暂无数据</p>
            </template>
            <el-table-column prop="dxxh" label="序号"> </el-table-column>
            <el-table-column
              prop="dwmc"
              :label="
                tableTabs.active == 2 || tableTabs.active == 4 ? '人员' : '单位'
              "
            >
              <template slot-scope="scope">
                {{ scope.row.dwmc }}
                <span @click="handleExpand(scope)">
                  <i
                    class="el-icon-arrow-right"
                    v-if="scope.row.isExpand === false"
                  ></i>
                  <i
                    class="el-icon-arrow-down"
                    v-if="scope.row.isExpand === true"
                  ></i>
                </span>
              </template>
            </el-table-column>
            <el-table-column prop="bkhajs" label="被考核案件数">
            </el-table-column>
            <el-table-column
              prop="fxwts"
              :label="
                tableTabs.active == 2 || tableTabs.active == 1
                  ? '发现问题数'
                  : '涉及案件类别'
              "
            >
            </el-table-column>
            <el-table-column
              prop="wtlx"
              :label="
                tableTabs.active == 3 || tableTabs.active == 4
                  ? '案件类别类型'
                  : '问题类型'
              "
            >
            </el-table-column>
            <el-table-column prop="sl" label="数量"> </el-table-column>
            <el-table-column prop="zb" label="占比">
              <template slot-scope="scope">
                {{
                  Math.round((scope.row.sl / scope.row.fxwts) * 10000) / 100 +
                    "%"
                }}
              </template>
            </el-table-column>
          </el-table>
          <div class="marginTop2" style="text-align: right;">
            <el-pagination
              :current-page="this.pageInfo.page"
              v-bind="pageInfo"
              @size-change="sizeChange"
              @current-change="getTbaleTjData"
            ></el-pagination>
          </div>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script>
import InfoTable from "../../tables/infoTable";
import Pie from "../../echarts/pie";
import { colorMorph } from "../../../controller/globalConfig";
import Ycontroller from "../../../controller/Ycontroller";
import DownExcel from "../../button/downExcel";
import BoxTabs from "../../tables/boxTabs";
import BoxName from "../../header/boxName";

export default {
  name: "zfjdtjfx",
  components: {
    Pie,
    InfoTable,
    DownExcel,
    BoxName,
    BoxTabs
  },
  props: {},
  data() {
    return {
      // 概括数据
      gkInfo: {
        khkpcs: 0,
        sjajs: 0,
        xsajs: 0,
        xzajs: 0,
        fxwts: 0,
        sjkhdw: 0
      },
      // 问题类型排名表格数据
      WtLxTable: {
        table: {
          data: [],
          height: "17rem",
          stripe: true
        },
        thead: [
          {
            type: "index",
            label: "序号",
            width: 80,
            align: "center"
          },
          {
            prop: "wtlx",
            label: "问题类型",
            align: "center"
          },
          {
            prop: "xh",
            label: "次数",
            width: 80,
            align: "center"
          },
          {
            prop: "zb",
            label: "占比",
            width: 80,
            align: "center"
          }
        ]
      },
      // tab栏切换
      tableTabs: {
        // 表格tab切换
        active: 1,
        arr: [
          {
            type: 1,
            label: "各单位案件问题指标统计"
          },
          {
            type: 2,
            label: "各民警案件问题指标统计"
          },
          {
            type: 3,
            label: "各单位案件类别统计"
          },
          {
            type: 4,
            label: "各民警案件类别统计"
          }
        ]
      },
      // 被考核单位情况分析单位表格数据
      radio: "1",
      type: 1, //控制请求的类型数据
      tableData: {
        rows: [],
        total: 0,
        pagesize: 1000,
        pageno: 1
      },
      // 用于暂时收纳返回的报表数据
      copyDataSource: [],
      // 分页信息
      pageInfo: {
        page: 1,
        pageSize: 10,
        pageSizes: [10, 20, 40],
        total: 0,
        layout: "total, sizes, prev, pager, next, jumper",
        background: true
      }
    };
  },
  computed: {
    // 案件类型占比
    caseTypeCanvas() {
      return {
        id: "caseTypeCanvas",
        name: "案件类型",
        tooltip: {
          trigger: "item",
          formatter: "{a}<br/>{b}:{c}"
        },
        radius: ["0%", "65%"],
        center: ["50%", "50%"],
        Xdata: ["刑事案件", "行政案件"],
        Ydata: [
          {
            name: "刑事案件",
            value: this.gkInfo.xsajs,
            colorT: colorMorph.abnormal.colorT,
            colorB: colorMorph.abnormal.colorB,
            itemStyle: {
              normal: {
                borderWidth: 6, // 间距的宽度
                borderColor: "#fff" //背景色
              }
            }
          },
          {
            name: "行政案件",
            value: this.gkInfo.xzajs,
            colorT: colorMorph.normal.colorT,
            colorB: colorMorph.normal.colorB,
            itemStyle: {
              normal: {
                borderWidth: 6, // 间距的宽度
                borderColor: "#fff" //背景色
              }
            }
          }
        ],
        label: {
          show: true,
          formatter: "{d}%", //饼图上展示的数据格式
          textStyle: {
            fontSize: 25,
            color: "#888888",
            padding: [0, 0, 0, -8] // 修改文字和图标距离
          },
          rich: {
            //圆点位置大小配置
            hr: {
              //auto自定义
              backgroundColor: "auto",
              borderRadius: 7,
              width: 15,
              height: 15,
              padding: [0, -30, 0, 0]
            },
            a: {
              padding: [-50, 10, -30, 10],
              color: "#888888",
              fontSize: 28,
              fontWeight: "bold",
              lineHeight: 5
            }
          }
        },
        //折线长度
        labelLine: {
          //第一段
          length: 17,
          //第二段
          length2: 25
        }
      };
    }
  },
  watch: {
    "$attrs.timeValue": {
      handler() {
        this.updateData();
      },
      deep: true
    },
    "$attrs.dwInfo": {
      handler() {
        this.updateData();
      },
      deep: true
    },
    tableTabs: {
      handler() {
        this.switchTableData();
      },
      deep: true
    },
    radio: {
      handler() {
        this.getTbaleTjData();
      },
      deep: true
    }
  },
  created() {
    if (this.$attrs.timeValue && this.$attrs.timeValue.length) {
      this.updateData();
    }
  },
  mounted() {
    this.init();
    this.tableData.rows = this.tableData.rows.map((item, i) => {
      const firstQuestionType = item.wtlxXqVOList[0];
      return {
        ...item,
        wtlx: firstQuestionType ? firstQuestionType.wtlx : "",
        sl: firstQuestionType ? firstQuestionType.sl : "",
        zb: firstQuestionType ? firstQuestionType.sl : "",
        isExpand: false, // 初始化默认箭头收起
        $idx: i // 为了记录带箭头的rowData是展开还是收起，从而影响是否展示隐藏的数据
      };
    });
    this.copyDataSource = [...this.tableData.rows];
  },
  methods: {
    init() {
      this.switchTableData();
    },
    /**
     * 分页改变
     */
    sizeChange(size) {
      this.pageInfo.pageSize = size;
      this.getTbaleTjData();
    },
    /**
     * 切换表格数据源
     */
    switchTableData() {
      if (this.tableTabs.active === 1) {
        this.type = 1;
      } else if (this.tableTabs.active === 2) {
        this.type = 2;
      } else if (this.tableTabs.active === 3) {
        this.type = 3;
      } else if (this.tableTabs.active === 4) {
        this.type = 4;
      }
      this.getTbaleTjData();
    },
    /**
     * 数据更新
     */
    updateData() {
      const [startTime, endTime] = this.$attrs["timeValue"];
      const dwInfo = this.$attrs["dwInfo"];
      console.log(dwInfo);
      const json = {
        bmbm: dwInfo.checked.unitid,
        startTime,
        endTime
      };
      // this.updateCountData(json); //获取顶部的概况数据并计算出饼图的百分比
      // this.getWtlxList(json); //获得问题类型排行的表格数据
      this.getTbaleTjData(); //获取表格的数据
    },
    /**
     * 更新统计概况数据
     * @param json
     */
    async updateCountData(json) {
      const gkInfo = this.gkInfo;
      for (let key in gkInfo) {
        gkInfo[key] = 0;
      }

      const res = await Ycontroller.khkp.getZftjsl(json);
      if (res.data.code === 200 && res.data.results) {
        this.gkInfo = res.data.results;
      } else {
        this.$message.error(
          "获取统计概括数据出错， 错误原因:" + res.data.message
        );
      }
    },
    /**
     * 更新问题类型排名分析表格
     * @param json
     */
    async getWtlxList(json) {
      const res = await Ycontroller.khkp.getWtlxList(json);
      this.WtLxTable.table.data = [];
      if (res.data.code === 200 && res.data.results) {
        this.WtLxTable.table.data = res.data.results;
      } else {
        this.$message.error(
          "获取问题类型数据出错，错误原因:" + res.data.message
        );
      }
    },
    /**
     * 获取报表数据,根据不同的类型来区分发送的是单位还是人员
     */
    async getTbaleTjData(spage = 1) {
      this.pageInfo.page = spage;
      const [startTime, endTime] = this.$attrs["timeValue"];
      const dwInfo = this.$attrs["dwInfo"];
      // type等于1或3的时候调用单位详情
      // 2或者4的时候调用民警维度详情接口
      if (this.type == 1 || this.type == 3) {
        // 一级单位的话则调用分局查询接口
        if (dwInfo.checked.unitlevel == 1) {
          const res = await Ycontroller.khkp.getFjdwxq({
            bmbm: dwInfo.checked.unitid,
            startTime,
            endTime,
            type: this.type == 1 ? 1 : 2,
            sPage: this.pageInfo.page,
            sSize: this.pageInfo.pageSize
          });
          this.tableData.rows = [];
          if (res.data.code === 200 && res.data.results) {
            this.tableData.rows = res.data.results.rows;
            this.pageInfo.total = res.data.results.total;
            console.log(this.tableData.rows, "表格数据");
          } else {
            this.$message.error(
              "获取报表数据出错，错误原因:" + res.data.message
            );
          }
          // 查询分局及其以下的派出所
        } else if (dwInfo.checked.unitlevel == 2) {
          const res = await Ycontroller.khkp.getPcsdwxq({
            bmbm: dwInfo.checked.unitid,
            startTime,
            endTime,
            type: this.type == 1 ? 1 : 2,
            sPage: this.pageInfo.page,
            sSize: this.pageInfo.pageSize
          });
          this.tableData.rows = [];
          if (res.data.code === 200 && res.data.results) {
            this.tableData.rows = res.data.results.rows;
            this.pageInfo.total = res.data.results.total;
            console.log(this.tableData.rows, "表格数据");
          } else {
            this.$message.error(
              "获取报表数据出错，错误原因:" + res.data.message
            );
          }
        }
      } else if (this.type == 2 || this.type == 4) {
        const res = await Ycontroller.khkp.getMjdwxq({
          bmbm: dwInfo.checked.unitid,
          startTime,
          endTime,
          type: this.type == 2 ? 1 : 2,
          sPage: this.pageInfo.page,
          sSize: this.pageInfo.pageSize,
          sfxxjr: this.radio == 2 ? "yes" : "No"
        });
        this.tableData.rows = [];
        if (res.data.code === 200 && res.data.results) {
          this.tableData.rows = res.data.results.rows;
          this.pageInfo.total = res.data.results.total;
          console.log(this.tableData.rows, "表格数据");
        } else {
          this.$message.error("获取报表数据出错，错误原因:" + res.data.message);
        }
      }
    },
    // //格式化成需要的数据
    // formartData(datas) {
    //   this.tableData.rows = this.tableData.rows.map((item, i) => {
    //     // 存储初始化数据格式，方便后面操作
    //     const expandColumns = Object.entries(item.wtlx || {}).map(
    //       ([key, value]) => {
    //         return { wtlx: key, sl: value };
    //       }
    //     );
    //     return {
    //       ...item,
    //       wtlx: expandColumns[0] ? expandColumns[0].wtlx : "",
    //       sl: expandColumns[0]
    //         ? expandColumns[0].sl
    //         : "",
    //       expandColumns,
    //       isExpand: false, // 初始化默认箭头收起
    //       $idx: i // 为了记录带箭头的rowData是展开还是收起，从而影响是否展示隐藏的数据
    //     };
    //   });
    //   this.copyDataSource = [...this.tableData.rows];
    //   console.log(this.copyDataSource, "复制起来的");
    // },
    // /**
    //  * 处理表格数据
    //  */
    // handleExpand(scope) {
    //   console.log(scope);
    //   let index = scope.$index;
    //   let column = scope.column.property;
    //   let rowData = scope.row;
    //   let newTableData = [];
    //   this.copyDataSource.forEach((item, i) => {
    //     if (index == i) {
    //       item.isExpand = item.isExpand == undefined ? true : !item.isExpand; //展开与收起
    //     }
    //     newTableData.push(item);
    //     if (
    //       index == i &&
    //       rowData.isExpand &&
    //       rowData.expandColumns &&
    //       rowData.expandColumns.length > 0
    //     ) {
    //       rowData.expandColumns.forEach(el => {
    //         let json = { ...rowData, ...el };
    //         newTableData.push(json);
    //       });
    //     }
    //   });
    //   this.tableData.rows = newTableData;
    //   console.log(this.tableData.rows, "AAAAA");
    // },
    // /**
    //  *合并表格数据
    //  */
    // objectSpanMethod({ row, column, rowIndex, columnIndex }) {
    //   let data = this.tableData.rows; //拿到当前table中数据
    //   let cellValue = row[column.property]; //当前位置的值
    //   let noSortArr = ["wtlx", "sl"]; //不需要合并的字段（不进行合并行的prop）
    //   if (cellValue && !noSortArr.includes(column.property)) {
    //     let prevRow = data[rowIndex - 1]; //获取到上一条数据
    //     let nextRow = data[rowIndex + 1]; //下一条数据
    //     if (prevRow && prevRow[column.property] === cellValue) {
    //       //当有上一条数据，并且和当前值相等时
    //       return { rowspan: 0, colspan: 0 };
    //     } else {
    //       let countRowspan = 1;
    //       while (nextRow && nextRow[column.property] === cellValue) {
    //         //当有下一条数据并且和当前值相等时,获取新的下一条
    //         nextRow = data[++countRowspan + rowIndex];
    //       }
    //       if (countRowspan > 1) {
    //         return { rowspan: countRowspan, colspan: 1 };
    //       }
    //     }
    //   }
    // }
    handleExpand(scope) {
      const newData = [];
      this.copyDataSource.forEach((item, i) => {
        const isExpand =
          scope.row.$idx === i
            ? (item.isExpand = !item.isExpand)
            : item.isExpand;
        newData.push(
          {
            ...item,
            isExpand
          },
          ...(isExpand ? item.wtlxXqVOList.slice(1) : []) // slice去重第一项
        );
      });
      this.tableData.rows = newData;
    },
    objectSpanMethod({ row, columnIndex }) {
      if ([0, 1, 2, 3].includes(columnIndex)) {
        if (row.isExpand === true) {
          const len = row.wtlxXqVOList.length;
          return {
            rowspan: len === 0 ? 1 : len,
            colspan: 1
          };
        } else if (row.isExpand === undefined) {
          return {
            rowspan: 0,
            colspan: 0
          };
        }
      }
    }
  }
};
</script>

<style scoped lang="less">
.zfjdtjfx {
  .flex-1 {
    flex: 1;
    flex-shrink: 1;
  }
  .flex-2 {
    flex: 2;
  }
  .canvas-area {
    // padding-bottom: 0.6rem;
    height: 100%;
    width: 80%;
  }
  .canvas-info-area {
    width: 20%;
    padding-left: 1.2rem;
    .click-notTable-num {
      font-size: 1.2rem;
    }
    .canvas-info-area-name {
      font-size: 0.9rem;
    }
    .canvas-info-area-dw {
      color: #666666;
      font-size: 0.7rem;
    }
  }
  .box-name {
    padding: 0.6rem;
  }
  .box-normal {
    height: 23rem;
    .tip {
      align-items: center;
      justify-content: center;
      div {
        width: 37.5rem;
        height: 2.5rem;
        background-color: #ededed;
        border-radius: 1rem;
        display: flex;
        align-items: center;
        justify-content: center;
        font-family: MicrosoftYaHei-Bold;
        font-size: 0.875rem;
        color: #f04864;
        font-weight: 500;
      }
    }
  }
  .topBox {
    width: 20%;
    background: #f2f3f3;
    height: 6.875rem;
    padding: 0.625rem;
    .icon {
      margin-right: 0.9375rem;
      img {
        width: 3.75rem;
        height: 3.6875rem;
      }
    }
    .titleNum {
      text-align: right;
      white-space: nowrap;
      .title {
        font-family: MicrosoftYaHei;
        font-size: 0.9375rem;
        color: #444444;
        margin-bottom: 0.4735rem;
      }
      .Num {
        font-family: Helvetica-Condensed;
        font-size: 1.2rem;
        color: #2572c5;
        border-bottom: 0.0625rem dashed #2572c5;
      }
    }
    .type {
      margin-left: 0.9375rem;
      .caseBox {
        height: 2rem;
        width: 18.75rem;
        padding: 0 0.9375rem;
        background-color: #ffffff;
        position: relative;
        span:nth-of-type(1) {
          margin-left: 2.8125rem;
          font-family: MicrosoftYaHei;
          font-size: 0.9375rem;
          color: #333333;
        }
        span:nth-of-type(1)::before {
          top: 30%;
          left: 5%;
          position: absolute;
          display: block;
          width: 1.125rem;
          height: 1.125rem;
          border-radius: 50%;
          background-image: linear-gradient(0deg, #49a3ff 0%, #dcf8ff 100%),
            linear-gradient(#3981ff, #3981ff);
          background-blend-mode: normal, normal;
          content: "";
        }
        .red:nth-of-type(1)::before {
          background-image: linear-gradient(0deg, #ec836a 0%, #fae2e5 100%),
            linear-gradient(#3981ff, #3981ff);
          background-blend-mode: normal, normal;
        }
        span:nth-of-type(2) {
          font-family: Helvetica-Condensed;
          font-size: 1.2rem;
          color: #2572c5;
          border-bottom: 0.0625rem dashed #2572c5;
        }
      }
    }
  }
  .topBox:nth-of-type(2) {
    width: 40%;
  }
  .content-line-3 {
    /deep/ .el-card__body {
      padding: 0;
    }
    .zsTable .el-table .colorRed {
      color: #79ace4 !important;
    }

    .zsTable .el-table thead .cell {
      white-space: pre-line;
      text-align: center !important;
    }

    .split {
      white-space: pre-line;
    }

    .zsTable tr.current-row {
      background-color: #deeeff !important;
    }

    td {
      background-color: inherit;
    }
    /deep/ .el-radio__label {
      font-size: 12px;
    }
  }
}
</style>
