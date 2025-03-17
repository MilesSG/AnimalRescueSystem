<template>
  <div class="found-container">
    <!-- 流浪动物列表 -->
    <div class="found-list-container">
      <div class="section-header">
        <i class="el-icon-discover"></i>
        <span>流浪动物信息</span>
        <div class="section-divider"></div>
      </div>
      
      <el-table 
        :data="tableData" 
        stripe 
        class="found-table"
        v-loading="loading">
        <el-table-column prop="nickname" label="动物名字">
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
        <el-table-column prop="status" label="状态" width="100">
          <template slot-scope="scope">
            <el-tag 
              size="medium" 
              :type="scope.row.status === '未领养' ? 'warning' : 'success'">
              {{ scope.row.status }}
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
      title="流浪动物信息" 
      :visible.sync="dialogFormVisible" 
      width="40%" 
      :close-on-click-modal="false"
      :destroy-on-close="true">
      <el-form :model="form" :rules="rules" ref="foundForm" label-width="120px" size="small">
        <el-form-item label="动物名字" prop="nickname">
          <el-input v-model="form.nickname" placeholder="请输入动物名字"></el-input>
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
        <el-form-item label="领养状态" prop="status">
          <el-radio-group v-model="form.status">
            <el-radio label="未领养">未领养</el-radio>
            <el-radio label="已领养">已领养</el-radio>
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
  name: "Found",
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
          { required: true, message: '请输入动物名字', trigger: 'blur' },
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
        status: [
          { required: true, message: '请选择领养状态', trigger: 'change' },
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
      this.request.get("/found/page", {
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
      this.$refs.foundForm.validate((valid) => {
        if (valid) {
          this.request.post("/found", this.form).then(res => {
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
.found-container {
  min-height: calc(100vh - 60px);
  padding: 20px;
  background-color: #f5f7fa;
}

.found-list-container {
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
  color: #67C23A;
}

.section-header span {
  font-size: 20px;
  font-weight: 600;
  color: #67C23A;
}

.section-divider {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 80px;
  height: 3px;
  background: linear-gradient(to right, #67C23A, #E6A23C);
  border-radius: 3px;
}

.found-table {
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