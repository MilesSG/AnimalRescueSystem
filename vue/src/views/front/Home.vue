<template>
  <div class="home-container">
    <!-- 视频展示区 -->
    <div class="video-section">
      <div class="video-wrapper">
        <video class="main-video" src="../../assets/animalvideo.mp4" muted autoplay controls loop></video>
        <div class="video-overlay">
          <h1 class="hero-title">关爱流浪动物 <span>共建和谐社会</span></h1>
          <p class="hero-subtitle">每一个生命都值得被尊重和关爱</p>
        </div>
      </div>
    </div>

    <!-- 公告区域 -->
    <div class="section notice-section">
      <div class="section-header">
        <i class="el-icon-bell"></i>
        <span>公告列表</span>
      </div>
      <el-collapse v-model="active" class="custom-collapse">
        <el-collapse-item :name="index + ''" v-for="(item, index) in notices" :key="'notice-'+index">
          <template slot="title">
            <div class="notice-title">
              <i class="el-icon-document"></i>
              <span>{{ item.name }}</span>
              <span class="notice-time">{{ item.time }}</span>
            </div>
          </template>
          <div class="notice-content">{{ item.content }}</div>
        </el-collapse-item>
      </el-collapse>
    </div>

    <!-- 流浪宠物展示区 -->
    <div class="section pet-section">
      <div class="section-header">
        <i class="el-icon-pet"></i>
        <span>流浪宠物</span>
        <div class="section-divider"></div>
      </div>
      
      <div class="pet-grid">
        <el-card 
          v-for="item in tableData" 
          :key="item.id" 
          class="pet-card"
          shadow="hover"
          @click.native="$router.push('/front/homeDetail?id=' + item.id)">
          <div class="pet-card-inner">
            <div class="pet-image-container">
              <img :src="fixImageUrl(item.img)" alt="" class="pet-image">
              <div class="pet-badge" :class="{'adoptable': item.adopt === '可领养'}">
                {{ item.adopt === '可领养' ? '可领养' : '不可领养' }}
              </div>
            </div>
            <div class="pet-info">
              <div class="pet-header">
                <h3 class="pet-name">{{ item.nickname }}</h3>
                <div class="pet-tags">
                  <el-tag size="mini" type="info">{{ item.sex }}</el-tag>
                  <el-tag size="mini" type="warning">{{ item.age }}</el-tag>
                </div>
              </div>
              <div class="pet-details">
                <div class="pet-detail-item">
                  <i class="el-icon-collection-tag"></i>
                  <span>{{ item.type }}</span>
                </div>
                <div class="pet-detail-item">
                  <i class="el-icon-location"></i>
                  <span>{{ item.address }}</span>
                </div>
                <div class="pet-detail-item">
                  <i class="el-icon-first-aid-kit"></i>
                  <span>{{ item.status }}</span>
                </div>
                <div class="pet-detail-item pet-description">
                  <i class="el-icon-document"></i>
                  <span>{{ item.information }}</span>
                </div>
              </div>
            </div>
          </div>
        </el-card>
      </div>
      
      <!-- 分页 -->
      <div class="pagination-container">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pageNum"
          :page-sizes="[2, 5, 10, 20]"
          :page-size="pageSize"
          background
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import { fixImageUrl } from '@/utils/request'

export default {
  name: "FrontHome",
  data() {
    return {
      tableData: [],
      total: 0,
      pageNum: 1,
      pageSize: 10,
      name: "",
      notices: [],
      active: '0'
    }
  },
  created() {
    this.load()
  },
  methods: {
    load() {
      this.request.get("/animal/page", {
        params: {
          pageNum: this.pageNum,
          pageSize: this.pageSize,
          name: this.name,
        }
      }).then(res => {
        this.tableData = res.data.records
        this.total = res.data.total
      })

      this.request.get("/notice/front").then(res => {
        this.notices = res.data
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
    fixImageUrl
  }
}
</script>

<style>
.home-container {
  padding-bottom: 40px;
  max-width: 1200px;
  margin: 0 auto;
}

/* 视频区域样式 */
.video-section {
  margin-bottom: 30px;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
}

.video-wrapper {
  position: relative;
  width: 100%;
  height: 0;
  padding-bottom: 56.25%; /* 16:9 比例 */
  overflow: hidden;
}

.main-video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.video-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: linear-gradient(to right, rgba(0, 0, 0, 0.7), rgba(0, 0, 0, 0.3));
  color: white;
  text-align: center;
  padding: 0 20px;
}

.hero-title {
  font-size: 2.5rem;
  font-weight: 700;
  margin-bottom: 15px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
  animation: fadeInDown 1s ease-out;
}

.hero-title span {
  color: #67C23A;
}

.hero-subtitle {
  font-size: 1.2rem;
  opacity: 0.9;
  max-width: 600px;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.5);
  animation: fadeInUp 1s ease-out 0.3s both;
}

/* 通用区块样式 */
.section {
  background-color: #fff;
  border-radius: 12px;
  padding: 25px;
  margin-bottom: 30px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.section:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.1);
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
  font-size: 24px;
  font-weight: 600;
  color: #3F5EFB;
  position: relative;
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

/* 公告区域样式 */
.notice-section {
  background: linear-gradient(135deg, #f8f9fa, #e9ecef);
}

.custom-collapse {
  border: none;
}

.notice-title {
  display: flex;
  align-items: center;
  font-size: 16px;
  color: #333;
}

.notice-title i {
  margin-right: 10px;
  color: #67C23A;
}

.notice-time {
  margin-left: auto;
  font-size: 12px;
  color: #999;
}

.notice-content {
  padding: 15px;
  background-color: rgba(103, 194, 58, 0.05);
  border-radius: 8px;
  color: #666;
  line-height: 1.6;
}

/* 宠物卡片网格 */
.pet-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
  margin-bottom: 30px;
}

.pet-card {
  border-radius: 12px;
  overflow: hidden;
  transition: all 0.3s ease;
  cursor: pointer;
  border: none;
}

.pet-card:hover {
  transform: translateY(-10px);
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.1);
}

.pet-card-inner {
  display: flex;
  flex-direction: column;
}

.pet-image-container {
  position: relative;
  height: 200px;
  overflow: hidden;
}

.pet-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.pet-card:hover .pet-image {
  transform: scale(1.1);
}

.pet-badge {
  position: absolute;
  top: 10px;
  right: 10px;
  padding: 5px 10px;
  background-color: #F56C6C;
  color: white;
  border-radius: 20px;
  font-size: 12px;
  font-weight: bold;
}

.pet-badge.adoptable {
  background-color: #67C23A;
}

.pet-info {
  padding: 15px;
}

.pet-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.pet-name {
  margin: 0;
  font-size: 18px;
  font-weight: 600;
  color: #333;
}

.pet-tags {
  display: flex;
  gap: 5px;
}

.pet-details {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.pet-detail-item {
  display: flex;
  align-items: center;
  font-size: 14px;
  color: #666;
}

.pet-detail-item i {
  margin-right: 8px;
  color: #3F5EFB;
  font-size: 16px;
}

.pet-description {
  margin-top: 5px;
  color: #888;
  font-size: 13px;
  line-height: 1.4;
  max-height: 60px;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
}

/* 分页容器 */
.pagination-container {
  display: flex;
  justify-content: center;
  margin-top: 30px;
}

/* 动画 */
@keyframes fadeInDown {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 自定义Element UI组件样式 */
.el-collapse-item__header {
  padding: 15px;
  border-radius: 8px;
  background-color: #f9f9f9;
  border: none;
  margin-bottom: 10px;
  transition: background-color 0.3s ease;
}

.el-collapse-item__header:hover {
  background-color: #f0f0f0;
}

.el-collapse-item__content {
  padding: 0 15px 15px;
}

.el-tag--mini {
  padding: 2px 6px;
  border-radius: 4px;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .pet-grid {
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  }
  
  .hero-title {
    font-size: 1.8rem;
  }
  
  .hero-subtitle {
    font-size: 1rem;
  }
}
</style>
