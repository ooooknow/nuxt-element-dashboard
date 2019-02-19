<template>
    <div class="index">
        <el-data-table ref="elDataTable" v-bind="tableConfig"></el-data-table>
    </div>
</template>

<script>
  export default {
    data() {
      return {
        tableConfig: {
          url: "/component",
          dataPath: "list",
          totalPath: "total_count",
          //列表数据对应
          columns: [
            {
              type: "selection"
            },
            {
              prop: "name",
              label: "组件名称"
            },
            {
              prop: "type",
              label: "分类"
            },
            {
              prop: "version",
              label: "版本"
            },
            {
              prop: "language",
              label: "语言"
            },
            {
              prop: "update_time",
              label: "最后更新时间",
              formatter: row => {
                const time = new Date(parseInt(row.update_time));
                return time.getFullYear() + "-" + time.getMonth() + 1 + "-" + time.getDate();
              }
            },
            {
              prop: "state",
              label: "状态",
              "class-name": "state-enable",
              formatter: row => (row.state === "1" ? "上架" : "下架")
            }
          ],
          //搜索表格
          searchForm: [
            {
              $type: "input",
              $id: "name",
              label: "组件名称",
              $el: {
                placeholder: "请输入"
              }
            },
            {
              $id: "type",
              $type: "select",
              label: "分类",
              $el: {
                placeholder: "请选择"
              },
              $options: [{
                label: "前端组件",
                value: "1"
              }, {
                label: "测试子组件",
                value: "2"
              }, {
                label: "分布式工具",
                value: "3"
              }, {
                label: "应用范围",
                value: "4"
              }, {
                label: "数据存储",
                value: "5"
              }]
            },
            {
              $id: "state",
              $type: "select",
              label: "状态",
              $el: {
                placeholder: "请选择"
              },
              $options: [{
                label: "下架",
                value: "0"
              }, {
                label: "上架",
                value: "1"
              }]
            }
          ],
          //添加及修改表单
          form: [
            {
              $type: "input",
              $id: "name",
              label: "用户名",
              rules: [
                {
                  required: true,
                  message: "请输入组件名",
                  trigger: "blur",
                  transform: v => v && v.trim()
                }
              ],
              $el: { placeholder: "请输入组件名" }
            },
            {
              $type: "select",
              $id: "type",
              label: "分类",
              rules: [{ required: true, message: "请选择分类", trigger: "blur" }],
              $options: [{
                label: "前端组件",
                value: "1"
              }, {
                label: "测试子组件",
                value: "2"
              }, {
                label: "分布式工具",
                value: "3"
              }, {
                label: "应用范围",
                value: "4"
              }, {
                label: "数据存储",
                value: "5"
              }],
              $el: {
                placeholder: "请选择"
              }
            },
            {
              $type: "input",
              $id: "version",
              label: "版本",
              rules: [
                {
                  required: true,
                  message: "请输入版本",
                  trigger: "blur",
                  transform: v => v && v.trim()
                }
              ],
              $el: { placeholder: "请输入版本" }
            },
            {
              $type: "input",
              $id: "language",
              label: "开发语言",
              rules: [
                {
                  required: true,
                  message: "请输入开发语言",
                  trigger: "blur",
                  transform: v => v && v.trim()
                }
              ],
              $el: { placeholder: "请输入开发语言" }
            }
          ],
          //表单样式
          formAttrs: { labelWidth: "80px" },
          extraButtons: [
            {
              type: "primary",
              text: "查看",
              atClick: row => {
                this.$refs.elDataTable.onDefaultView(row);
                return Promise.resolve(false);
              }
            }, {
              text: "编辑",
              atClick: row => {
                this.$refs.elDataTable.onDefaultEdit(row);
                return Promise.resolve();
              }
            }, {
              text: "上架",
              show: row => {
                return row.state !== "1";
              },
              atClick: row => {
                this.$confirm("确认将组件 " + row.name + " 上架吗", "提示", {
                  type: "warning",
                  beforeClose: (action, instance, done) => {
                    if (action === "confirm") {
                      instance.confirmButtonLoading = true;
                      let data = row;
                      data.state = "1";
                      this.$axios.put(this.tableConfig.url + "/" + row.id, data)
                        .then(resp => {
                          instance.confirmButtonLoading = false;
                          done();
                          this.$refs.elDataTable.showMessage(true);
                          this.$refs.elDataTable.cancel();
                        })
                        .catch(err => {
                          instance.confirmButtonLoading = false;
                        });
                    } else done();
                  }
                }).catch(er => {
                  /*取消*/
                });
                return Promise.resolve();
              }
            },
            {
              text: "下架",
              show: row => {
                return row.state === "1";
              },
              atClick: row => {
                this.$confirm("确认将组件 " + row.name + " 下架吗", "提示", {
                  type: "warning",
                  beforeClose: (action, instance, done) => {
                    if (action === "confirm") {
                      instance.confirmButtonLoading = true;
                      let data = row;
                      data.state = "0";
                      this.$axios.put(this.tableConfig.url + "/" + row.id, data)
                        .then(resp => {
                          instance.confirmButtonLoading = false;
                          done();
                          this.$refs.elDataTable.showMessage(true);
                          this.$refs.elDataTable.cancel();
                        })
                        .catch(err => {
                          instance.confirmButtonLoading = false;
                        });
                    } else done();
                  }
                }).catch(er => {
                  /*取消*/
                });
                return Promise.resolve();
              }
            }

          ],
          hasEdit: false,
          tableAttrs: {
            "cell-style": ({ row, column }) => {
              if (column.property === "state") {
                if (row.state === "1") {
                  return "color:#00CD00";
                }
              }
            }
          }
        },
        form: [
          {
            rules: [{ required: true, trigger: "blur" }],
            $el: {
              placeholder: "请输入品牌名称"
            },
            label: "品牌名称",
            $id: "name",
            $type: "input"
          },
          {
            rules: [
              { message: "请输入品牌别名", required: false, trigger: "blur" }
            ],
            $el: { placeholder: "请输入品牌别名" },
            label: "品牌别名",
            $id: "alias",
            $type: "input"
          }
        ]
      };
    },
    methods: {}
  };
</script>
<style lang="stylus">
    .index {
        .home-img {
            width: 100%;
        }
    }
</style>
