<template>
  <div class="outDiv">
    <div class="searchBar">
      <div class="searchLeft">
        <el-popover placement="right-bottom" title="条件检索" trigger="click" v-model="searchVisible">
          <searchForm @startSearch="handleTableSearch" @cancleSearch="handleCancleSeach"></searchForm>
          <el-button slot="reference" size="mini">按条件筛选</el-button>
        </el-popover>
        <navTopTag :searchTagList="searchTagList" @handleDelOne="handleDelOne"></navTopTag>
      </div>
    </div>
    <div>
      <el-tabs v-model="allTableObj.currentTab" @tab-click="tabClick($event)">
        <el-tab-pane label="所有数据" name="all"></el-tab-pane>
        <el-tab-pane label="搜索结果" name="search"></el-tab-pane>
      </el-tabs>
      <div>
        <BaseTable
          :columns="allTableObj.columns"
          :currentObj="allTableObj.currentObj"
          :showPage="allTableObj.showPage"
          :options="allTableObj.options"
          :loadObj="allTableObj.loadObj"
          :operationColumn="allTableObj.operationColumn"
          @dataSizeChange="handleSizeChange($event)"
          @dataCurrentChange="handleCurrentChange($event)"
        ></BaseTable>
      </div>
    </div>
  </div>
</template>
<script>
import searchForm from "./list-search";
export default {
  inject: ["reload"],
  components: {
    searchForm
  },
  data() {
    return {
      searchTagList: [],
      searchVisible: false,
      allTableObj: {
        showPage: true,
        columns: [
          {
            prop: "num",
            label: "#",
            width: "50px",
            align: "center"
          },
          {
            prop: "a",
            label: "老人姓名",
            align: "center",
            route: true
          },
          {
            prop: "b",
            label: "床位号",
            align: "center"
          },
          {
            prop: "c",
            label: "陪同人员姓名",
            align: "center"
          },
          {
            prop: "d",
            label: "陪同人员类型",
            align: "center"
          },
          {
            prop: "e",
            label: "陪同人员电话",
            align: "center"
          },
          {
            prop: "f",
            label: "陪同人员身份证",
            align: "center"
          },
          {
            prop: "g",
            label: "陪同人员姓名",
            align: "center"
          },
          {
            prop: "g",
            label: "外出时间",
            align: "center"
          },
          {
            prop: "h",
            label: "计划返回时间",
            align: "center"
          },
          {
            prop: "i",
            label: "外出原因",
            align: "center"
          }
        ],
        currentObj: {
          dataList: [
            {
              num: 1,
              a: "陈义初",
              b: "B栋-101-01",
              c: "吴青峰",
              d: "家属",
              e: "15689352014",
              f: "362322198103251476",
              g: "2019-08-21 09:21:00",
              h: "2019-08-21 18:00:00",
              i: "参加志愿者活动"
            },
            {
              num: 2,
              a: "郭守敬",
              b: "B栋-101-01",
              c: "周青松",
              d: "朋友",
              e: "15689352014",
              f: "362322198103251476",
              g: "2019-08-21 09:21:00",
              h: "2019-08-21 18:00:00",
              i: "探亲"
            },
            {
              num: 3,
              a: "刘一红",
              b: "B栋-101-01",
              c: "庆余年",
              d: "邻居",
              e: "15689352014",
              f: "362322198103251476",
              g: "2019-08-21 09:21:00",
              h: "2019-08-21 18:00:00",
              i: "去医院"
            },
            {
              num: 4,
              a: "孙望",
              b: "B栋-101-01",
              c: "孙晓红",
              d: "朋友",
              e: "15689352014",
              f: "362322198103251476",
              g: "2019-08-21 09:21:00",
              h: "2019-08-21 18:00:00",
              i: "去医院"
            },
            {
              num: 5,
              a: "周洲",
              b: "B栋-101-01",
              c: "周强",
              d: "亲属",
              e: "15689352014",
              f: "362322198103251476",
              g: "2019-08-21 09:21:00",
              h: "2019-08-21 18:00:00",
              i: "探亲"
            },
            {
              num: 6,
              a: "吴国旺",
              b: "B栋-101-01",
              c: "李子涛",
              d: "亲属",
              e: "15689352014",
              f: "362322198103251476",
              g: "2019-08-21 09:21:00",
              h: "2019-08-21 18:00:00",
              i: "参加家庭聚会"
            }
          ],
          currentPage: 1,
          pageSize: 20,
          total: 0
        },
        options: {
          stripe: true,
          highlightCurrentRow: true,
          headStyleMethod: this.getTableHead
        },
        loadObj: {
          isLoading: false
        },
        allDataNow: {
          currentPage: 1,
          pageSize: 20,
          dataList: [],
          total: 0
        },
        searchDataNow: {
          currentPage: 1,
          pageSize: 20,
          dataList: [],
          total: 0
        },
        operationColumn: {
          show: true,
          align: "center",
          width: "200px",
          btns: [
            {
              size: "mini",
              text: "详情",
              type: "text",
              style: "font-size:14px",
              disabled: false,
              method: (index, row) => {
                this.handleDetail(index, row);
              }
            }
          ]
        },
        currentTab: "all"
      }
    };
  },
  methods: {
    add() {
      this.$router.push({ name: "residence-register-live-add" });
    },
    handleTableSearch(i) {
      this.searchVisible = i.searchShow;
      this.searchTagList = this.$searchTag.getTagList(
        this.$searchNameList.getSameOldSearch(),
        i.searchData
      );
      this.$store.commit("setSearchForm", i.searchData);
      this.allTableObj.currentTab = "search";
      const reqObj = {
        index: 0,
        size: 20,
        ...i.searchData
      };
      this.loadData(reqObj);
    },
    handleCancleSeach(i) {
      this.searchVisible = i.searchShow;
    },
    handleDelOne(i) {
      //减少一个检索条件时
      this.searchTagList = this.searchTagList.filter((item, index) => {
        if (item[0] === i[0]) {
          return false;
        } else {
          return true;
        }
      });
      const obj = this.$searchTag.deleteOneSearch(i[0], this.currentSearchForm);
      if (this.$baseFunc.paramsValidate(obj)) {
        this.allTableObj.searchDataNow.dataList = [];
        this.allTableObj.searchDataNow.currentPage = 1;
        this.allTableObj.searchDataNow.pageSize = 20;
        this.allTableObj.searchDataNow.total = 0;
        this.allTableObj.currentTab = "all";
        this.allTableObj.showPage = false;
        this.allTableObj.currentObj = { ...this.allTableObj.allDataNow };
        this.$nextTick(() => {
          this.allTableObj.showPage = true;
        });
      } else {
        const reqObj = {
          index: 0,
          size: 20,
          ...obj
        };
        this.loadData(reqObj);
      }
      this.$store.commit("setSearchForm", obj);
    },
    tabClick(i) {
      if (i.name == "all") {
        this.allTableObj.showPage = false;
        this.allTableObj.currentObj = { ...this.allTableObj.allDataNow };
        this.$nextTick().then(() => {
          this.allTableObj.showPage = true;
        });
      }
      if (i.name == "search") {
        this.allTableObj.showPage = false;
        this.allTableObj.currentObj = { ...this.allTableObj.searchDataNow };
        this.$nextTick().then(() => {
          this.allTableObj.showPage = true;
        });
      }
    },
    loadData(reqObj) {
      this.allTableObj.loadObj.isLoading = true;
      getSameHouseOldersApi(reqObj)
        .then(res => {
          if (res.code === 0) {
            const temp = {
              dataList: res.data.map((item, idx) => ({
                num: res.index * res.size + idx + 1,
                ...item
              })),
              currentPage: res.index + 1,
              total: res.total,
              pageSize: res.size
            };
            this.allTableObj.currentObj = { ...temp };
            if (this.allTableObj.currentTab === "all") {
              this.allTableObj.allDataNow = { ...temp };
            }
            if (this.allTableObj.currentTab === "search") {
              this.allTableObj.searchDataNow = { ...temp };
            }
          } else {
            this.$message.error(`获取数据失败${res.des}`);
          }
        })
        .catch(() => {})
        .finally(() => {
          this.allTableObj.loadObj.isLoading = false;
        });
    },
    //每次页面码数变了 要变回第一页
    handleSizeChange(i) {
      if (
        this.$baseFunc.isEmptyObj(this.currentSearchForm) &&
        this.allTableObj.currentTab === "search"
      ) {
        this.$message.error("检索条件不能为空");
        return false;
      }
      let reqObj = {};
      let tab = this.allTableObj.currentTab;
      if (tab === "all") {
        this.allTableObj.allDataNow.currentPage = 1;
        this.allTableObj.allDataNow.pageSize = i;
        reqObj = {
          index: 0,
          size: i
        };
      } else if (tab === "search") {
        this.allTableObj.searchDataNow.currentPage = 1;
        this.allTableObj.searchDataNow.pageSize = i;
        reqObj = {
          index: 0,
          size: i,
          ...this.currentSearchForm
        };
      }
      this.loadData(reqObj);
    },
    handleCurrentChange(i) {
      if (
        this.$baseFunc.isEmptyObj(this.currentSearchForm) &&
        this.allTableObj.currentTab === "search"
      ) {
        this.$message.error("检索条件不能为空");
        return;
      }
      let reqObj = {};
      let tab = this.allTableObj.currentTab;
      if (tab == "all") {
        this.allTableObj.allDataNow.currentPage = i;
        reqObj = {
          index: i - 1,
          size: this.allTableObj.allDataNow.pageSize
        };
      } else if (flag == "search") {
        this.allTableObj.searchDataNow.currentPage = i;
        reqObj = {
          index: i - 1,
          size: this.allTableObj.searchDataNow.pageSize,
          ...this.currentSearchForm
        };
      }
      this.loadData(reqObj);
    },
    handleDetail(i, j) {
      console.log(i, j);
    }
  },
  mounted() {
    // this.loadData({ index: 0, size: 20 })
  }
};
</script>

