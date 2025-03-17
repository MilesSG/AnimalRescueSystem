<template>
  <div class="activity-container">
    <!-- 搜索区域 -->
    <div class="search-container">
      <div class="search-box">
        <el-input 
          placeholder="请输入活动名称" 
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
      <el-button type="primary" icon="el-icon-plus" @click="handleAdd">新增活动</el-button>
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
        <el-table-column prop="name" label="标题"></el-table-column>
        <el-table-column prop="content" label="内容" width="100">
          <template v-slot="scope">
            <el-button size="mini" type="primary" icon="el-icon-view" @click="view(scope.row.content)">查看</el-button>
          </template>
        </el-table-column>
        <el-table-column prop="time" label="时间" width="160"></el-table-column>
        <el-table-column label="图片" width="120">
          <template slot-scope="scope">
            <el-image 
              class="activity-image" 
              :src="fixImageUrl(scope.row.img)" 
              :preview-src-list="[fixImageUrl(scope.row.img)]"
              fit="cover">
            </el-image>
          </template>
        </el-table-column>
        <el-table-column prop="num" label="报名人数" width="100">
          <template slot-scope="scope">
            <el-tag type="success">{{ scope.row.num }} 人</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="address" label="地址" show-overflow-tooltip></el-table-column>

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
              title="确定删除该活动吗？"
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
      :title="form.id ? '编辑活动信息' : '新增活动信息'" 
      :visible.sync="dialogFormVisible" 
      width="70%" 
      :close-on-click-modal="false"
      :destroy-on-close="true">
      <el-form :model="form" :rules="rules" ref="activityForm" label-width="100px" size="small">
        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="标题" prop="name">
              <el-input v-model="form.name" placeholder="请输入活动标题"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="时间" prop="time">
              <el-date-picker 
                v-model="form.time" 
                type="datetime" 
                value-format="yyyy-MM-dd HH:mm:ss" 
                placeholder="选择活动时间">
              </el-date-picker>
            </el-form-item>
          </el-col>
        </el-row>
        
        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="报名人数" prop="num">
              <el-input-number v-model="form.num" :min="0" :max="1000" placeholder="请输入报名人数"></el-input-number>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="地址" prop="address">
              <el-input v-model="form.address" placeholder="请输入活动地址"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        
        <el-form-item label="封面" prop="img">
          <el-upload
            class="avatar-uploader"
            :action="request.defaults.baseURL + '/file/upload'"
            :show-file-list="false"
            :on-success="handleImgUploadSuccess"
            :before-upload="beforeAvatarUpload"
          >
            <img v-if="form.img" :src="fixImageUrl(form.img)" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
          <div class="upload-tip">建议上传 16:9 比例的图片，大小不超过2MB</div>
        </el-form-item>
        
        <el-form-item label="内容" prop="content">
          <mavon-editor 
            ref="md" 
            v-model="form.content" 
            :ishljs="true" 
            @imgAdd="imgAdd"
            style="min-height: 400px"
          />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="save">确 定</el-button>
      </div>
    </el-dialog>

    <!-- 内容查看对话框 -->
    <el-dialog title="活动内容详情" :visible.sync="contentVisible" width="70%">
      <div v-html="contentHtml" class="content-preview"></div>
    </el-dialog>
  </div>
</template>

<script>
import { fixImageUrl } from '@/utils/request'

export default {
  name: "Activity",
  data() {
    return {
      tableData: [],
      total: 0,
      pageNum: 1,
      pageSize: 10,
      name: "",
      form: {},
      dialogFormVisible: false,
      contentVisible: false,
      contentHtml: '',
      multipleSelection: [],
      loading: false,
      rules: {
        name: [
          { required: true, message: '请输入活动标题', trigger: 'blur' },
          { min: 2, max: 50, message: '长度在 2 到 50 个字符' },
        ],
        time: [
          { required: true, message: '请选择活动时间', trigger: 'change' },
        ],
        num: [
          { required: true, message: '请输入报名人数', trigger: 'blur' },
          { type: 'number', message: '报名人数必须为数字' },
        ],
        address: [
          { required: true, message: '请输入活动地址', trigger: 'blur' },
          { min: 5, max: 100, message: '长度在 5 到 100 个字符' },
        ],
        content: [
          { required: true, message: '请输入活动内容', trigger: 'blur' },
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
      this.request.get("/activity/page", {
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
      this.$refs.activityForm.validate((valid) => {
        if (valid) {
          this.request.post("/activity", this.form).then(res => {
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
      this.form = {
        time: new Date().toISOString().slice(0, 19).replace('T', ' '),
        num: 0
      }
    },
    handleEdit(row) {
      this.form = JSON.parse(JSON.stringify(row))
      this.dialogFormVisible = true
    },
    del(id) {
      this.request.delete("/activity/" + id).then(res => {
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
      this.request.post("/activity/del/batch", ids).then(res => {
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
    view(content) {
      this.contentHtml = marked(content)
      this.contentVisible = true
    },
    imgAdd(pos, $file) {
      // 第一步：将图片上传到服务器
      var formdata = new FormData()
      formdata.append('file', $file)
      this.request.post('/file/upload', formdata, {
        headers: { 'Content-Type': 'multipart/form-data' }
      }).then(res => {
        // 第二步：将返回的url替换到文本原位置
        this.$refs.md.$img2Url(pos, fixImageUrl(res))
      })
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
.activity-container {
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

.activity-image {
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

.content-preview {
  padding: 20px;
  background-color: #fff;
  border-radius: 8px;
  max-height: 600px;
  overflow-y: auto;
}

.content-preview img {
  max-width: 100%;
  height: auto;
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
