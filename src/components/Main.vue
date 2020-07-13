<template >
  <el-container class="hello" style="background-color: #CCFFCC">
    <el-header>
      <h2>swagger-showdoc 文档生成器</h2>
    </el-header>
    <el-main>
      <el-form :inline="true" :model="showDoc" class="demo-form-inline">
        <el-row :gutter="20">
          <el-col :span="5">
            <el-form-item label="showDoc地址:">
              <el-input v-model="showDoc.url" placeholder="www.showdoc.cc" @blur="saveData"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="9">
            <el-form-item label="apiKey:" style="width: 50%">
              <el-input
                v-model="showDoc.apiKey"
                placeholder="项目设置->开放api"
                style="width: 200%"
                @blur="saveData"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="9">
            <el-form-item label="apiToken:" style="width: 50%">
              <el-input
                v-model="showDoc.apiToken"
                placeholder="项目设置->开放api"
                style="width: 200%"
                @blur="saveData"
              ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <el-table :data="tableData" :row-class-name="rowClassName" stripe>
        <el-table-column prop="module" label="目录">
          <template scope="scope">
            <el-input v-model="scope.row.module" placeholder="请输入内容" @blur="saveData"></el-input>
          </template>
        </el-table-column>
        <el-table-column label="swagger地址">
          <template scope="scope">
            <el-input v-model="scope.row.ip" placeholder="请输入内容" @blur="saveData">
              <template slot="prepend">Http://</template>
            </el-input>
          </template>
        </el-table-column>
        <el-table-column prop="port" label="端口">
          <template scope="scope">
            <el-input v-model="scope.row.port" type="number" placeholder="请输入内容" @blur="saveData"></el-input>
          </template>
        </el-table-column>
        <el-table-column prop="path" label="路径">
          <template scope="scope">
            <el-input v-model="scope.row.path" placeholder="请输入内容" @blur="saveData"></el-input>
          </template>
        </el-table-column>
        <el-table-column prop="address" label="操作">
          <template scope="scope">
            <el-button @click="add" icon="el-icon-plus"></el-button>
            <el-button @click="sub(scope.row.index)" icon="el-icon-minus"></el-button>
          </template>
        </el-table-column>
      </el-table>
      <br />
      <el-button @click="submit" :loading="loading" align="center">点击同步</el-button>
      <br />
    </el-main>
  </el-container>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      input: "",
      module: "1",
      loading: false,
      ip: "",
      port: "",
      path: "",
      showDoc: {
        url: "www.showdoc.cc",
        apiKey:'',
        apiToken:'',
      },
      tableData: [
        {
          module: "",
          ip: "",
          port: "",
          path: ""
        }
      ],
      dengmiQueryForm: [{}]
    };
  },
  methods: {
    add() {
      this.tableData.push({});
    },
    sub(index) {
      var array = this.tableData;
      if (array.length > 1) {
        for (var i = 0; i < array.length; i++) {
          if (array[i].index === index) {
            array.splice(i, 1);
            break;
          }
        }
      }
    },
    submit: function() {
      let sel = this;
      this.$confirm("此操作将覆盖showdoc文档, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          // eslint-disable-next-line no-undef
          this.loading = true;
          this.axios({
            // 格式a
            method: "post",
            url: process.env.VUE_APP_BASE_URL +"/updateShowDoc",
            data: {
              showDocConfig: this.showDoc,
              swaggerConfigList: this.tableData
            }
          })
            .then(function(resp) {
              sel.loading = false;
              if (resp.data.succeed) {
                sel.$message({
                  type: "success",
                  message: "同步成功"
                });
              } else {
                sel.$message({
                  type: "error",
                  message: resp.data.message
                });
              }
            })
            .catch(resp => {
              this.loading = false;
              this.$message({
                type: "error",
                message: resp
              });
            });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消"
          });
        });
    },
    getData() {
      var showDoc = window.localStorage.getItem("showDoc");
      var tableData = window.localStorage.getItem("tableData");
      if (tableData != "null" &&tableData) {
        this.tableData = JSON.parse(tableData);
      }
      if (showDoc != "null" &&tableData) {
        this.showDoc = JSON.parse(showDoc);
      }
    },
    saveData() {
      window.localStorage.setItem("showDoc", JSON.stringify(this.showDoc));
      window.localStorage.setItem("tableData", JSON.stringify(this.tableData));
    },
    rowClassName({ row, rowIndex }) {
      row.index = rowIndex;
      console.log(row);
    }
  },
  mounted: function() {
    this.getData();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.el-row {
  margin-bottom: 20px;
}
.el-col {
  border-radius: 4px;
}

.bg-purple-dark {
  background: #99a9bf;
}

.bg-purple {
  background: #d3dce6;
}

.bg-purple-light {
  background: #e5e9f2;
}

.grid-content {
  border-radius: 4px;
  min-height: 36px;
}

.el-header,
.el-footer {
  background-color: #b3c0d1;
  color: #333;
  text-align: center;
}

.el-aside {
  background-color: #d3dce6;
  color: #333;
  text-align: center;
  line-height: 200px;
}

.el-main {
  background-color: #e9eef3;
  color: #333;
  text-align: center;
}

.el-container.el-main.el {
}

body > .el-container {
  margin-bottom: 0px;
}

.el-container:nth-child(5) .el-aside,
.el-container:nth-child(6) .el-aside {
  line-height: 260px;
}

.el-container:nth-child(7) .el-aside {
  line-height: 320px;
}
</style>
