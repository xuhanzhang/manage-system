<template>
  <div class=" documentation-container">
    <!-- 步骤条区域 -->
      <div class="step-style">
        <el-steps :space="400" :active="activeIndex - 0" finish-status="success" align-center>
          <el-step title="添加任务"></el-step>
          <el-step title="添加样品"></el-step>
          <el-step title="选取人员"></el-step>
          <el-step title="完成"></el-step>
        </el-steps>
      </div>
    <!-- tab栏区域 -->
    <div class="tab-style">
      <el-form ref="addFormRef" label-width="120px" :rules="addFormRules" :model="task">
        <el-tabs v-model="activeIndex" :tab-position="'left'">
          <el-tab-pane label="添加任务" name="0" class="tab-task">
            <el-form-item label="任务名称" prop="name">
              <el-input v-model="task.name"></el-input>
            </el-form-item>
            <el-form-item label="评价方法" prop="methodId">
              <el-select v-model="task.methodId" placeholder="请选择" style="width: 100%;">
                <el-option label="香料评价" value="1"></el-option>
                <el-option label="单体评价" value="2"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item :label="'气\u2003\u2003压'" prop="bar">
              <el-input v-model="task.bar"></el-input>
            </el-form-item>

            <el-form-item label="开始时间" prop="startTime">
              <el-date-picker type="date" format="yyyy-MM-dd" value-format="yyyy-MM-dd" placeholder="选择日期"
                              v-model="task.startTime" style="width: 100%;"></el-date-picker>
            </el-form-item>

            <el-form-item label="是否两轮" prop="isD">
              <div>
                <el-select v-model="task.isD" placeholder="请选择" style="width: 100%;">
                  <el-option label="否" value="1"></el-option>
                  <el-option label="是" value="2"></el-option>
                </el-select>
              </div>

            </el-form-item>
            <el-form-item :label="'湿\u2003\u2003度'" prop="rh">
              <el-input v-model="task.rh"></el-input>
            </el-form-item>

            <el-form-item label="截至时间" prop="endTime">
              <el-date-picker type="date" format="yyyy-MM-dd" value-format="yyyy-MM-dd" placeholder="选择日期"
                              v-model="task.endTime" style="width: 100%;"></el-date-picker>
            </el-form-item>

            <el-form-item label="备注信息" prop="notes">
              <el-input v-model="task.notes" clearable></el-input>
            </el-form-item>
          </el-tab-pane>
          <el-tab-pane label="添加样品" name="1">
            <!-- 渲染表单的Item项 -->
            <el-form-item>
              <div class="sample-info">
                <div class="btn-style">
                  <el-button type="primary" icon="el-icon-circle-plus-outline" @click="addSampleBtn">新增</el-button>
                  <el-button type="danger" icon="el-icon-remove-outline" @click="delSampleBtn">删除</el-button>
                </div>
                <el-table
                  ref="sampleTable"
                  :data="task.sampleList"
                  border
                  class="tableBox"
                  @row-click="rowBtn"
                >
                  <el-table-column
                    type="selection"
                    width="55"
                    header-align="center"
                    align="center">
                  </el-table-column>
                  <el-table-column label="序号"
                                   header-align="center"
                                   align="center"
                                   width="60px"
                                   type="index"
                                   :index="indexMethod">
                  </el-table-column>
                  <el-table-column v-if="false"
                                   prop="id"
                                   label="ID"
                                   header-align="center"
                                   align="center">
                  </el-table-column>
                  <el-table-column
                    prop="name"
                    label="样品名称"
                    header-align="center"
                    align="center">
                  </el-table-column>

                  <el-table-column
                    prop="cas"
                    label="样品CAS"
                    header-align="center"
                    align="center">
                  </el-table-column>

                  <el-table-column
                    label="样品规格"
                    header-align="center"
                    align="center">
                  </el-table-column>
                  <el-table-column
                    label="备注信息"
                    header-align="center"
                    align="center">
                  </el-table-column>

                  <el-table-column label="操作"
                                   width="150"
                                   header-align="center"
                                   align="center">
                    <template slot-scope="scope">
                      <el-button
                        size="mini"
                        type="primary"
                        @click="handleEdit(scope.$index, scope.row)">编辑
                      </el-button>
                      <el-button
                        size="mini"
                        type="danger"
                        @click="handleDelete(scope.$index, scope.row)">删除
                      </el-button>
                    </template>
                  </el-table-column>
                </el-table>
              </div>
            </el-form-item>
          </el-tab-pane>
          <el-tab-pane label="选取人员" name="2">
            <el-form-item>
              <div class="transfer-info">
                <el-transfer
                    v-model="task.evaluatorList"
                    :props="{key: 'id',label: 'name'}"
                    :titles="['未关联', '已关联']"
                    @change="handleChange"
                    :data="evaluators"
                  >
                  </el-transfer>
                <div class="commitBtn-style">
                  <el-button type="danger"  icon="el-icon-remove-outline" @click="taskCommit">任务提交</el-button>
                </div>
                </div>
            </el-form-item>
          </el-tab-pane>
        </el-tabs>
      </el-form>
    </div>
    <el-dialog title="提示" :visible.sync="dialogVisible">
      <el-form  :model="sample" label-width="80px">
        <el-row :gutter="10">
          <el-col :span="12">
            <el-form-item label="样品名称" prop="name">
              <el-input v-model="sample.name"></el-input>
            </el-form-item>
            <el-form-item label="样品CAS" prop="cas">
              <el-input v-model="sample.cas"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="样品规格" prop="spec">
              <el-input v-model="sample.spec"></el-input>
            </el-form-item>
            <el-form-item label="备注信息" prop="notes">
              <el-input v-model="sample.notes"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <span slot="footer" class="dialog-footer">
      <el-button @click="dialogVisible = false">取 消</el-button>
      <el-button type="primary" @click="sampleCommit">确 定</el-button>
    </span>
    </el-dialog>
  </div>

</template>

<script>

export default {
  name: 'Documentation',
  data() {
    return {
      dialogVisible:false,
      activeIndex: '0',
      sample: {
        name:'',
        cas:'',
        spec:'',
        notes:''
      },
      selections:{},
      evaluators:[],
      task: {
        operator: '',
        name: '',
        methodId: '',
        startTime: '',
        endTime: '',
        isD: '',
        bar: '',
        rh: '',
        notes: '',
        sampleList: [{name: 'x1', cas: '80', spec: '25', notes: '135'},
          {name: 's2', cas: '80', spec: '25', notes: '120'}
        ],
        evaluatorList: [1, 2, 3, 4, 5, 6]
        // 添加商品的表单数据对象
      },
      addFormRules: {
        name: [
          { required: true, message: '任务名称不能为空！', trigger: 'blur' }
        ],
        methodId: [
          { required: true, message: '请选择评价方法！', trigger: 'change'}
        ],
        startTime: [
          { required: true, message: '请选择开始时间！', trigger: 'blur' }
        ],
        endTime: [
          { required: true, message: '请选择截至时间', trigger: 'blur' }
        ],
        isD: [
          { required: true, message: '请选择请评价次数', trigger: 'blur'}
        ]
      },
    }
  },
  created() {
  },
  methods: {
    //添加表格索引
    indexMethod(index) {
      return index + 1;
    },
    sampleCommit() {
    },
    rowBtn(row, col, event) {
      row.flags = !row.flags;
      this.$refs.sampleTable.toggleRowSelection(row, row.flags);
    },
    handleEdit(index, row) {
    },
    handleDelete() {
    },
    addSampleBtn() {
      this.dialogVisible = true;
    },
    delSampleBtn() {
    },
    // 勾选时候，记录[{id: row}, ...]
    onSelect(selections, row) {
      if (this.selections[row.id]) {
        delete this.selections[row.id];
      } else {
        this.selections[row.id] = row;
      }
    },
    // 全选情况
    onSelectAll(selections) {
      // 全选
      if (selections.length) {
        selections.forEach(row => {
          this.selections[row.id] = row;
        })
      } else {
        // 取消全选
        selections.forEach(row => {
          delete this.selections[row.id];
        })
      }
    },
    handleChange(value, direction, movedKeys) {
      console.log(value, direction, movedKeys);
      //可以通过direction回调right/left 来进行操作，right：把数字移到右边，left把数据移到左边
      if (direction === "right") {
        console.log(this.task.evaluatorList);
      }
      if (direction === "left") {
        console.log(this.task.evaluatorList);
      }
    },
    taskCommit(){

    }
  }
}
</script>

<style lang="scss">
.documentation-container {
  margin: 50px 50px 0 50px;
  display: flex;
  display: -webkit-flex; /* Safari */
  align-items:center;/*指定垂直居中*/
  justify-content: center;
  flex-wrap: wrap;
  .step-style{
    justify-content: center;
    width: 80%;
    .el-steps{
      justify-content: center;
      width: 100%;
    }
  }
  .tab-style{
    margin-top: 50px;
    justify-content: center;
    width: 60%;
    align-items:center;
    .sample-info{
      margin-left: -30px;
      .btn-style{
        margin-bottom: 5px;
      }
    }
    .commitBtn-style{
      margin: 0 auto;
      text-align: center;
      margin-top: 50px;
    }
  }
}
</style>
