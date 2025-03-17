<template>
  <div class="rescue-container">
    <!-- 搜索区域 -->
    <div class="search-container">
      <div class="search-box">
        <el-input 
          placeholder="请输入名称" 
          prefix-icon="el-icon-search" 
          v-model="name"
          clearable>
        </el-input>
        
        <div class="search-buttons">
          <el-button type="primary" icon="el-icon-search" @click="load">搜索</el-button>
          <el-button icon="el-icon-refresh" @click="reset">重置</el-button>
        </div>
      </div>
    </div>

    <!-- 操作按钮区域 -->
    <div class="action-container">
      <el-button type="primary" icon="el-icon-plus" @click="handleAdd">新增救助点</el-button>
      <el-popconfirm
        confirm-button-text='确定'
        cancel-button-text='取消'
        icon="el-icon-warning"
        icon-color="red"
        title="确定批量删除这些数据吗？"
        @confirm="delBatch"
      >
        <el-button type="danger" icon="el-icon-delete" slot="reference">批量删除</el-button>
      </el-popconfirm>
    </div>

    <!-- 表格区域 -->
    <div class="table-container">
      <el-table 
        :data="tableData" 
        border 
        stripe 
        :header-cell-class-name="'table-header'"
        @selection-change="handleSelectionChange"
        v-loading="loading">
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column prop="id" label="ID" width="80" sortable></el-table-column>
        <el-table-column prop="name" label="名称"></el-table-column>
        <el-table-column prop="addres" label="地址"></el-table-column>
        <el-table-column label="图片" width="120">
          <template slot-scope="scope">
            <el-image 
              class="rescue-image" 
              :src="fixImageUrl(scope.row.img)" 
              :preview-src-list="[fixImageUrl(scope.row.img)]"
              fit="cover">
            </el-image>
          </template>
        </el-table-column>
        <el-table-column prop="person" label="联系人"></el-table-column>
        <el-table-column prop="phone" label="联系方式"></el-table-column>
        <el-table-column prop="information" label="相关描述" show-overflow-tooltip></el-table-column>

        <el-table-column label="操作" width="180" align="center" fixed="right">
          <template slot-scope="scope">
            <el-button 
              type="primary" 
              icon="el-icon-edit" 
              size="mini" 
              @click="handleEdit(scope.row)">
              编辑
            </el-button>
            <el-popconfirm
              confirm-button-text='确定'
              cancel-button-text='取消'
              icon="el-icon-warning"
              icon-color="red"
              title="确定删除该记录吗？"
              @confirm="del(scope.row.id)"
            >
              <el-button 
                type="danger" 
                icon="el-icon-delete" 
                size="mini" 
                slot="reference">
                删除
              </el-button>
            </el-popconfirm>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <!-- 分页区域 -->
    <div class="pagination-container">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="pageNum"
        :page-sizes="[10, 20, 50, 100]"
        :page-size="pageSize"
        background
        layout="total, sizes, prev, pager, next, jumper"
        :total="total">
      </el-pagination>
    </div>

    <!-- 表单对话框 -->
    <el-dialog 
      :title="form.id ? '编辑救助点信息' : '新增救助点信息'" 
      :visible.sync="dialogFormVisible" 
      width="40%" 
      :close-on-click-modal="false"
      :destroy-on-close="true">
      <el-form :model="form" :rules="rules" ref="rescueForm" label-width="100px" size="small">
        <el-form-item label="名称" prop="name">
          <el-input v-model="form.name" placeholder="请输入名称"></el-input>
        </el-form-item>
        <el-form-item label="地址" prop="addres">
          <el-input v-model="form.addres" placeholder="请输入地址"></el-input>
        </el-form-item>
        <el-form-item label="照片" prop="img">
          <el-upload 
            class="avatar-uploader"
            :action="request.defaults.baseURL + '/file/upload'" 
            :show-file-list="false"
            :on-success="handleImgUploadSuccess"
            :before-upload="beforeAvatarUpload">
            <img v-if="form.img" :src="fixImageUrl(form.img)" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </el-form-item>
        <el-form-item label="联系人" prop="person">
          <el-input v-model="form.person" placeholder="请输入联系人"></el-input>
        </el-form-item>
        <el-form-item label="联系方式" prop="phone">
          <el-input v-model="form.phone" placeholder="请输入联系方式"></el-input>
        </el-form-item>
        <el-form-item label="相关描述" prop="information">
          <el-input type="textarea" v-model="form.information" :rows="3" placeholder="请输入相关描述"></el-input>
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
import { fixImageUrl } from '@/utils/request'

export default {
  name: "Rescue",
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
        name: [
          { required: true, message: '请输入名称', trigger: 'blur' },
          { min: 2, max: 50, message: '长度在 2 到 50 个字符' },
        ],
        addres: [
          { required: true, message: '请输入地址', trigger: 'blur' },
          { min: 5, max: 100, message: '长度在 5 到 100 个字符' },
        ],
        person: [
          { required: true, message: '请输入联系人', trigger: 'blur' },
          { min: 2, max: 20, message: '长度在 2 到 20 个字符' },
        ],
        phone: [
          { required: true, message: '请输入联系方式', trigger: 'blur' },
          { pattern: /^1[3-9]\d{9}$/, message: '请输入正确的手机号码' },
        ],
        information: [
          { required: true, message: '请输入相关描述', trigger: 'blur' },
          { min: 5, max: 200, message: '长度在 5 到 200 个字符' },
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
      this.request.get("/rescue/page", {
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
      this.$refs.rescueForm.validate((valid) => {
        if (valid) {
          this.request.post("/rescue", this.form).then(res => {
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
    handleAdd() {
      this.dialogFormVisible = true
      this.form = {}
    },
    handleEdit(row) {
      this.form = JSON.parse(JSON.stringify(row))
      this.dialogFormVisible = true
    },
    del(id) {
      this.request.delete("/rescue/" + id).then(res => {
        if (res.code === '200') {
          this.$message.success("删除成功")
          this.load()
        } else {
          this.$message.error("删除失败")
        }
      })
    },
    handleSelectionChange(val) {
      this.multipleSelection = val
    },
    delBatch() {
      if (!this.multipleSelection.length) {
        this.$message.warning("请选择需要删除的数据")
        return
      }
      
      let ids = this.multipleSelection.map(v => v.id)
      this.request.post("/rescue/del/batch", ids).then(res => {
        if (res.code === '200') {
          this.$message.success("批量删除成功")
          this.load()
        } else {
          this.$message.error("批量删除失败")
        }
      })
    },
    reset() {
      this.name = ""
      this.load()
    },
    handleSizeChange(pageSize) {
      this.pageSize = pageSize
      this.load()
    },
    handleCurrentChange(pageNum) {
      this.pageNum = pageNum
      this.load()
    },
    handleImgUploadSuccess(res) {
      this.form.img = res
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === 'image/jpeg'
      const isPNG = file.type === 'image/png'
      const isLt2M = file.size / 1024 / 1024 < 2

      if (!isJPG && !isPNG) {
        this.$message.error('上传图片只能是 JPG 或 PNG 格式!')
      }
      if (!isLt2M) {
        this.$message.error('上传图片大小不能超过 2MB!')
      }
      return (isJPG || isPNG) && isLt2M
    },
    fixImageUrl
  }
}
</script>

<style>
.rescue-container {
  padding: 20px;
  background-color: #f5f7fa;
  min-height: calc(100vh - 50px);
}

.search-container {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.05);
  margin-bottom: 20px;
}

.search-box {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 15px;
}

.search-box .el-input {
  width: 220px;
}

.search-buttons {
  margin-left: auto;
  display: flex;
  gap: 10px;
}

.action-container {
  margin-bottom: 20px;
  display: flex;
  gap: 10px;
}

.table-container {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.05);
  margin-bottom: 20px;
}

.table-header {
  background-color: #f0f2f5 !important;
  color: #303133;
  font-weight: bold;
}

.rescue-image {
  width: 80px;
  height: 80px;
  border-radius: 4px;
  object-fit: cover;
}

.pagination-container {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.avatar-uploader {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  width: 178px;
  height: 178px;
}

.avatar-uploader:hover {
  border-color: #409EFF;
}

.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}

.avatar {
  width: 178px;
  height: 178px;
  display: block;
  object-fit: cover;
}

.dialog-footer {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}

@media (max-width: 768px) {
  .search-box {
    flex-direction: column;
    align-items: flex-start;
  }
  
  .search-box .el-input {
    width: 100%;
  }
  
  .search-buttons {
    margin-left: 0;
    width: 100%;
    justify-content: flex-end;
  }
}
</style>
