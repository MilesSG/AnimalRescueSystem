<template>
  <div class="animal-container">
    <!-- 搜索区域 -->
    <div class="search-container">
      <div class="search-box">
        <el-input 
          placeholder="请输入名称" 
          prefix-icon="el-icon-search" 
          v-model="name"
          clearable>
        </el-input>
        
        <el-select 
          v-model="adopt" 
          placeholder="领养状态" 
          clearable
          class="status-select">
          <el-option 
            v-for="item in ['不可领养', '可领养']" 
            :key="item" 
            :label="item" 
            :value="item">
          </el-option>
        </el-select>
        
        <div class="search-buttons">
          <el-button type="primary" icon="el-icon-search" @click="load">搜索</el-button>
          <el-button icon="el-icon-refresh" @click="reset">重置</el-button>
        </div>
      </div>
    </div>

    <!-- 操作按钮区域 -->
    <div class="action-container">
      <el-button type="primary" icon="el-icon-plus" @click="handleAdd">新增动物</el-button>
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
        <el-table-column prop="nickname" label="动物名字"></el-table-column>
        <el-table-column prop="sex" label="性别" width="80"></el-table-column>
        <el-table-column prop="type" label="种类"></el-table-column>
        <el-table-column prop="age" label="年龄" width="80"></el-table-column>
        <el-table-column label="图片" width="120">
          <template slot-scope="scope">
            <el-image 
              class="animal-image" 
              :src="fixImageUrl(scope.row.img)" 
              :preview-src-list="[fixImageUrl(scope.row.img)]"
              fit="cover">
            </el-image>
          </template>
        </el-table-column>
        <el-table-column prop="address" label="活动范围"></el-table-column>
        <el-table-column prop="status" label="身体状态"></el-table-column>
        <el-table-column prop="sterilization" label="是否绝育" width="100"></el-table-column>
        <el-table-column prop="vaccine" label="疫苗接种" width="100"></el-table-column>
        <el-table-column prop="adopt" label="可领养状态" width="100">
          <template slot-scope="scope">
            <el-tag :type="scope.row.adopt === '可领养' ? 'success' : 'info'">
              {{ scope.row.adopt }}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="isAdopt" label="是否被领养" width="100">
          <template slot-scope="scope">
            <el-tag :type="scope.row.isAdopt === '是' ? 'warning' : 'info'">
              {{ scope.row.isAdopt }}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="information" label="其他描述" show-overflow-tooltip></el-table-column>

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
      :title="form.id ? '编辑动物信息' : '新增动物信息'" 
      :visible.sync="dialogFormVisible" 
      width="40%" 
      :close-on-click-modal="false"
      :destroy-on-close="true">
      <el-form :model="form" :rules="rules" ref="animalForm" label-width="100px" size="small">
        <el-form-item label="动物名字" prop="nickname">
          <el-input v-model="form.nickname" placeholder="请输入动物名字"></el-input>
        </el-form-item>
        <el-form-item label="动物性别" prop="sex">
          <el-radio-group v-model="form.sex">
            <el-radio label="公">公</el-radio>
            <el-radio label="母">母</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="种类" prop="type">
          <el-input v-model="form.type" placeholder="请输入种类"></el-input>
        </el-form-item>
        <el-form-item label="年龄" prop="age">
          <el-input v-model="form.age" placeholder="请输入年龄"></el-input>
        </el-form-item>
        <el-form-item label="动物照片" prop="img">
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
        <el-form-item label="活动范围" prop="address">
          <el-input v-model="form.address" placeholder="请输入活动范围"></el-input>
        </el-form-item>
        <el-form-item label="身体状态" prop="status">
          <el-input v-model="form.status" placeholder="请输入身体状态"></el-input>
        </el-form-item>
        <el-form-item label="是否绝育" prop="sterilization">
          <el-select v-model="form.sterilization" placeholder="请选择">
            <el-option label="是" value="是"></el-option>
            <el-option label="否" value="否"></el-option>
            <el-option label="未知" value="未知"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="疫苗接种" prop="vaccine">
          <el-select v-model="form.vaccine" placeholder="请选择">
            <el-option label="已接种" value="已接种"></el-option>
            <el-option label="未接种" value="未接种"></el-option>
            <el-option label="未知" value="未知"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="可领养状态" prop="adopt">
          <el-select v-model="form.adopt" placeholder="请选择">
            <el-option label="可领养" value="可领养"></el-option>
            <el-option label="不可领养" value="不可领养"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="是否被领养" prop="isAdopt">
          <el-select v-model="form.isAdopt" placeholder="请选择">
            <el-option label="是" value="是"></el-option>
            <el-option label="否" value="否"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="其他描述" prop="information">
          <el-input type="textarea" v-model="form.information" :rows="3" placeholder="请输入其他描述"></el-input>
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
  name: "Animal",
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
      user: localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")) : {},
      adopt: '',
      loading: false,
      rules: {
        nickname: [
          { required: true, message: '请输入动物名字', trigger: 'blur' },
          { min: 2, max: 20, message: '长度在 2 到 20 个字符' },
        ],
        sex: [
          { required: true, message: '请选择动物性别', trigger: 'change' },
        ],
        type: [
          { required: true, message: '请输入种类', trigger: 'blur' },
          { min: 2, max: 20, message: '长度在 2 到 20 个字符' },
        ],
        age: [
          { required: true, message: '请输入年龄', trigger: 'blur' },
          { type: 'number', message: '年龄必须是数字' },
        ],
        address: [
          { required: true, message: '请输入活动范围', trigger: 'blur' },
          { min: 2, max: 100, message: '长度在 2 到 100 个字符' },
        ],
        status: [
          { required: true, message: '请输入身体状态', trigger: 'blur' },
          { min: 2, max: 100, message: '长度在 2 到 100 个字符' },
        ],
        sterilization: [
          { required: true, message: '请选择是否绝育', trigger: 'change' },
        ],
        vaccine: [
          { required: true, message: '请选择疫苗接种', trigger: 'change' },
        ],
        adopt: [
          { required: true, message: '请选择可领养状态', trigger: 'change' },
        ],
        isAdopt: [
          { required: true, message: '请选择是否被领养', trigger: 'change' },
        ],
        information: [
          { required: true, message: '请输入其他描述', trigger: 'blur' },
          { min: 2, max: 200, message: '长度在 2 到 200 个字符' },
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
      this.request.get("/animal/page", {
        params: {
          pageNum: this.pageNum,
          pageSize: this.pageSize,
          name: this.name,
          adopt: this.adopt,
        }
      }).then(res => {
        this.tableData = res.data.records
        this.total = res.data.total
        this.loading = false
      })
    },
    save() {
      this.$refs.animalForm.validate((valid) => {
        if (valid) {
          this.request.post("/animal", this.form).then(res => {
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
      this.$nextTick(() => {
        if(this.$refs.img) {
           this.$refs.img.clearFiles();
         }
         if(this.$refs.file) {
          this.$refs.file.clearFiles();
         }
      })
    },
    handleEdit(row) {
      this.form = JSON.parse(JSON.stringify(row))
      this.dialogFormVisible = true
       this.$nextTick(() => {
         if(this.$refs.img) {
           this.$refs.img.clearFiles();
         }
         if(this.$refs.file) {
          this.$refs.file.clearFiles();
         }
       })
    },
    del(id) {
      this.request.delete("/animal/" + id).then(res => {
        if (res.code === '200') {
          this.$message.success("删除成功")
          this.load()
        } else {
          this.$message.error("删除失败")
        }
      })
    },
    handleSelectionChange(val) {
      console.log(val)
      this.multipleSelection = val
    },
    delBatch() {
      if (!this.multipleSelection.length) {
        this.$message.error("请选择需要删除的数据")
        return
      }
      let ids = this.multipleSelection.map(v => v.id)  // [{}, {}, {}] => [1,2,3]
      this.request.post("/animal/del/batch", ids).then(res => {
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
      this.adopt = ""
      this.load()
    },
    handleSizeChange(pageSize) {
      console.log(pageSize)
      this.pageSize = pageSize
      this.load()
    },
    handleCurrentChange(pageNum) {
      console.log(pageNum)
      this.pageNum = pageNum
      this.load()
    },
    handleFileUploadSuccess(res) {
      this.form.file = res
    },
    handleImgUploadSuccess(res) {
      this.form.img = res
    },
    download(url) {
      window.open(url)
    },
    exp() {
      window.open("http://localhost:9090/animal/export")
    },
    handleExcelImportSuccess() {
      this.$message.success("导入成功")
      this.load()
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === 'image/jpeg'
      const isPNG = file.type === 'image/png'
      const isLt2M = file.size / 1024 / 1024 < 2

      if (!isJPG && !isPNG) {
        this.$message.error('上传头像图片只能是 JPG 或 PNG 格式!')
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!')
      }
      return isJPG && isLt2M
    },
  }
}
</script>

<style>
.animal-container {
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

.status-select {
  width: 150px;
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

.animal-image {
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
  
  .search-box .el-input,
  .status-select {
    width: 100%;
  }
  
  .search-buttons {
    margin-left: 0;
    width: 100%;
    justify-content: flex-end;
  }
}
</style>
