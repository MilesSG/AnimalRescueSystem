<template>
  <div class="lost-container">
    <!-- 丢失宠物列表 -->
    <div class="lost-list-container">
      <div class="section-header">
        <i class="el-icon-help"></i>
        <span>丢失/寻回宠物信息</span>
        <div class="section-divider"></div>
      </div>
      
      <el-table 
        :data="tableData" 
        stripe 
        class="lost-table"
        v-loading="loading">
        <el-table-column prop="nickname" label="宠物名字">
          <template slot-scope="scope">
            <div class="pet-name">{{ scope.row.nickname }}</div>
          </template>
        </el-table-column>
        <el-table-column prop="type" label="种类" width="100">
          <template slot-scope="scope">
            <el-tag size="medium" type="info">{{ scope.row.type }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="sex" label="性别" width="80">
          <template slot-scope="scope">
            <el-tag 
              size="medium" 
              :type="scope.row.sex === '公' ? 'primary' : 'danger'">
              {{ scope.row.sex }}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="person" label="联系人" width="100"></el-table-column>
        <el-table-column prop="phone" label="联系方式" width="120"></el-table-column>
        <el-table-column prop="status1" label="状态" width="100">
          <template slot-scope="scope">
            <el-tag 
              size="medium" 
              :type="scope.row.status1 === '已丢失' ? 'danger' : 'warning'">
              {{ scope.row.status1 }}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="status2" label="找回状态" width="100">
          <template slot-scope="scope">
            <el-tag 
              size="medium" 
              :type="scope.row.status2 === '已找到' ? 'success' : 'info'">
              {{ scope.row.status2 }}
            </el-tag>
          </template>
        </el-table-column>
      </el-table>
      
      <!-- 分页 -->
      <div class="pagination-container">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pageNum"
          :page-sizes="[5, 10, 20, 50]"
          :page-size="pageSize"
          background
          layout="total, prev, pager, next"
          :total="total">
        </el-pagination>
      </div>
    </div>

    <!-- 表单对话框 -->
    <el-dialog 
      title="丢失/寻回宠物信息" 
      :visible.sync="dialogFormVisible" 
      width="40%" 
      :close-on-click-modal="false"
      :destroy-on-close="true">
      <el-form :model="form" :rules="rules" ref="lostForm" label-width="120px" size="small">
        <el-form-item label="宠物名字" prop="nickname">
          <el-input v-model="form.nickname" placeholder="请输入宠物名字"></el-input>
        </el-form-item>
        <el-form-item label="种类" prop="type">
          <el-input v-model="form.type" placeholder="请输入种类"></el-input>
        </el-form-item>
        <el-form-item label="性别" prop="sex">
          <el-radio-group v-model="form.sex">
            <el-radio label="公">公</el-radio>
            <el-radio label="母">母</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="联系人" prop="person">
          <el-input v-model="form.person" placeholder="请输入联系人"></el-input>
        </el-form-item>
        <el-form-item label="联系方式" prop="phone">
          <el-input v-model="form.phone" placeholder="请输入联系方式"></el-input>
        </el-form-item>
        <el-form-item label="已丢失/待领回" prop="status1">
          <el-radio-group v-model="form.status1">
            <el-radio label="已丢失">已丢失</el-radio>
            <el-radio label="待领回">待领回</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="找回状态" prop="status2">
          <el-radio-group v-model="form.status2">
            <el-radio label="未找到">未找到</el-radio>
            <el-radio label="已找到">已找到</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="save">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "Lost",
  data() {
    return {
      tableData: [],
      total: 0,
      pageNum: 1,
      pageSize: 10,
      name: "",
      form: {},
      dialogFormVisible: false,
      multipleSelection: [],
      loading: false,
      rules: {
        nickname: [
          { required: true, message: '请输入宠物名字', trigger: 'blur' },
        ],
        type: [
          { required: true, message: '请输入种类', trigger: 'blur' },
        ],
        sex: [
          { required: true, message: '请选择性别', trigger: 'change' },
        ],
        person: [
          { required: true, message: '请输入联系人', trigger: 'blur' },
        ],
        phone: [
          { required: true, message: '请输入联系方式', trigger: 'blur' },
          { pattern: /^1[3-9]\d{9}$/, message: '请输入正确的手机号码' },
        ],
        status1: [
          { required: true, message: '请选择状态', trigger: 'change' },
        ],
        status2: [
          { required: true, message: '请选择找回状态', trigger: 'change' },
        ],
      }
    }
  },
  created() {
    this.load()
  },
  methods: {
    load() {
      this.loading = true
      this.request.get("/lost/page", {
        params: {
          pageNum: this.pageNum,
          pageSize: this.pageSize,
          name: this.name,
        }
      }).then(res => {
        this.tableData = res.data.records
        this.total = res.data.total
        this.loading = false
      })
    },
    save() {
      this.$refs.lostForm.validate((valid) => {
        if (valid) {
          this.request.post("/lost", this.form).then(res => {
            if (res.code === '200') {
              this.$message.success("保存成功")
              this.dialogFormVisible = false
              this.load()
            } else {
              this.$message.error("保存失败")
            }
          })
        } else {
          this.$message.error("请填写完整表单")
        }
      })
    },
    handleSizeChange(pageSize) {
      this.pageSize = pageSize
      this.load()
    },
    handleCurrentChange(pageNum) {
      this.pageNum = pageNum
      this.load()
    },
    handleSelectionChange(val) {
      this.multipleSelection = val
    }
  }
}
</script>

<style scoped>
.lost-container {
  min-height: calc(100vh - 60px);
  padding: 20px;
  background-color: #f5f7fa;
}

.lost-list-container {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.05);
  padding: 20px;
  margin-bottom: 20px;
}

.section-header {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  position: relative;
  padding-bottom: 15px;
}

.section-header i {
  font-size: 24px;
  margin-right: 10px;
  color: #3F5EFB;
}

.section-header span {
  font-size: 20px;
  font-weight: 600;
  color: #3F5EFB;
}

.section-divider {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 80px;
  height: 3px;
  background: linear-gradient(to right, #3F5EFB, #67C23A);
  border-radius: 3px;
}

.lost-table {
  margin-bottom: 20px;
  border-radius: 8px;
  overflow: hidden;
}

.pet-name {
  font-weight: 600;
  color: #303133;
}

.pagination-container {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.dialog-footer {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}
</style>
