<template>
  <div class="rescue-container">
    <!-- 救助站列表 -->
    <div class="rescue-list-container">
      <div class="section-header">
        <i class="el-icon-first-aid-kit"></i>
        <span>救助站信息</span>
        <div class="section-divider"></div>
      </div>
      
      <el-table 
        :data="tableData" 
        stripe 
        class="rescue-table"
        v-loading="loading">
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
        <el-table-column prop="name" label="名称">
          <template slot-scope="scope">
            <div class="rescue-name">{{ scope.row.name }}</div>
          </template>
        </el-table-column>
        <el-table-column prop="addres" label="地址" show-overflow-tooltip></el-table-column>
        <el-table-column prop="person" label="联系人" width="100"></el-table-column>
        <el-table-column prop="phone" label="联系方式" width="120"></el-table-column>
        <el-table-column prop="information" label="相关描述" show-overflow-tooltip></el-table-column>
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
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
        </el-pagination>
      </div>
    </div>

    <!-- 表单对话框 -->
    <el-dialog 
      title="救助站信息" 
      :visible.sync="dialogFormVisible" 
      width="40%" 
      :close-on-click-modal="false"
      :destroy-on-close="true">
      <el-form :model="form" :rules="rules" ref="rescueForm" label-width="100px" size="small">
        <el-form-item label="名称" prop="name">
          <el-input v-model="form.name" placeholder="请输入救助站名称"></el-input>
        </el-form-item>
        <el-form-item label="地址" prop="addres">
          <el-input v-model="form.addres" placeholder="请输入地址"></el-input>
        </el-form-item>
        <el-form-item label="图片" prop="img">
          <el-upload 
            class="avatar-uploader"
            :action="request.defaults.baseURL + '/file/upload'" 
            :show-file-list="false"
            :on-success="handleImgUploadSuccess"
            :before-upload="beforeAvatarUpload">
            <img v-if="form.img" :src="fixImageUrl(form.img)" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
          <div class="upload-tip">建议上传正方形图片，大小不超过2MB</div>
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
          { required: true, message: '请输入救助站名称', trigger: 'blur' },
          { min: 2, max: 50, message: '长度在 2 到 50 个字符' },
        ],
        addres: [
          { required: true, message: '请输入地址', trigger: 'blur' },
          { min: 5, max: 100, message: '长度在 5 到 100 个字符' },
        ],
        person: [
          { required: true, message: '请输入联系人', trigger: 'blur' },
        ],
        phone: [
          { required: true, message: '请输入联系方式', trigger: 'blur' },
        ],
      }
    }
  },
  created() {
    this.load()
  },
  methods: {
    fixImageUrl,
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
        this.$message.error('上传图片大小不超过 2MB!')
      }
      return (isJPG || isPNG) && isLt2M
    },
    handleSelectionChange(val) {
      this.multipleSelection = val
    }
  }
}
</script>

<style scoped>
.rescue-container {
  min-height: calc(100vh - 60px);
  padding: 20px;
  background-color: #f5f7fa;
}

.rescue-list-container {
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

.rescue-table {
  margin-bottom: 20px;
  border-radius: 8px;
  overflow: hidden;
}

.rescue-image {
  width: 80px;
  height: 80px;
  border-radius: 4px;
  object-fit: cover;
}

.rescue-name {
  font-weight: 600;
  color: #303133;
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

.upload-tip {
  font-size: 12px;
  color: #909399;
  margin-top: 5px;
}

.dialog-footer {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}
</style>
