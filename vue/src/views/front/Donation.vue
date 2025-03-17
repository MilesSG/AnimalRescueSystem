<template>
  <div class="donation-container donate-container">
    <div class="donation-header">
      <i class="el-icon-present"></i>
      <span>爱心捐赠</span>
      <div class="header-divider"></div>
    </div>
    
    <div class="donation-content">
      <!-- 关于捐赠 -->
      <div class="donation-card">
        <div class="donation-card-title">
          <i class="el-icon-info"></i>
          <span>关于捐赠</span>
        </div>
        <div class="donation-card-content">
          <p>您的每一份爱心都将用于改善流浪动物的生活条件，提供必要的医疗救助和日常照料，我们承诺将每一笔捐款都用于动物救助事业，并定期公开所有款项使用情况。</p>
          
          <p>我们目前急需的物资包括：</p>
          
          <div class="donation-items">
            <div class="donation-item">
              <div class="donation-item-icon">
                <i class="el-icon-food" style="font-size: 40px; color: #67C23A;"></i>
              </div>
              <div class="donation-item-name">宠物食品</div>
              <div class="donation-item-desc">猫粮、狗粮、幼犬/幼猫粮</div>
            </div>
            
            <div class="donation-item">
              <div class="donation-item-icon">
                <i class="el-icon-first-aid-kit" style="font-size: 40px; color: #F56C6C;"></i>
              </div>
              <div class="donation-item-name">医疗用品</div>
              <div class="donation-item-desc">消毒液、绷带、药品等</div>
            </div>
            
            <div class="donation-item">
              <div class="donation-item-icon">
                <i class="el-icon-house" style="font-size: 40px; color: #409EFF;"></i>
              </div>
              <div class="donation-item-name">生活用品</div>
              <div class="donation-item-desc">毛毯、猫砂、宠物窝等</div>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 联系方式 -->
      <div class="donation-card">
        <div class="donation-card-title">
          <i class="el-icon-phone"></i>
          <span>联系方式</span>
        </div>
        <div class="contact-info">
          <div>
            <div class="contact-item">
              <i class="el-icon-user"></i>
              <span>联系人：张三</span>
            </div>
            <div class="contact-item">
              <i class="el-icon-phone-outline"></i>
              <span>联系电话：010-65998877</span>
            </div>
          </div>
          <div>
            <div class="contact-item">
              <i class="el-icon-message"></i>
              <span>邮箱：support@animalrescue.org</span>
            </div>
            <div class="contact-item">
              <i class="el-icon-location-outline"></i>
              <span>办公地址：北京市海淀区中关村南大街5号</span>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 捐赠方式 -->
      <div class="donation-card">
        <div class="donation-card-title">
          <i class="el-icon-money"></i>
          <span>捐赠方式</span>
        </div>
        
        <div class="donation-tabs">
          <el-tabs v-model="activeName">
            <el-tab-pane label="现金捐赠" name="cash">
              <div class="payment-methods">
                <div class="payment-method">
                  <div class="payment-qrcode wechat-qrcode">
                    <i class="el-icon-s-platform" style="font-size: 120px; color: #07C160;"></i>
                    <div>扫码微信支付</div>
                  </div>
                  <div class="payment-method-name">微信支付</div>
                </div>
                <div class="payment-method">
                  <div class="payment-qrcode alipay-qrcode">
                    <i class="el-icon-s-platform" style="font-size: 120px; color: #1677FF;"></i>
                    <div>扫码支付宝支付</div>
                  </div>
                  <div class="payment-method-name">支付宝</div>
                </div>
              </div>
            </el-tab-pane>
            <el-tab-pane label="物资捐赠" name="goods">
              <p>您可以通过以下方式进行物资捐赠：</p>
              <ul>
                <li>直接送至我们的办公地址</li>
                <li>邮寄至我们的办公地址</li>
                <li>联系我们安排上门取件</li>
              </ul>
              <p>如需捐赠大件物资，请提前与我们联系，谢谢！</p>
            </el-tab-pane>
            <el-tab-pane label="志愿服务" name="volunteer">
              <p>我们欢迎您加入志愿者队伍，提供以下服务：</p>
              <ul>
                <li>日常照料：喂食、清洁、遛狗等</li>
                <li>医疗协助：陪同就医、术后照料等</li>
                <li>宣传推广：线上宣传、线下活动等</li>
                <li>寄养家庭：临时收养待领养的动物</li>
              </ul>
              <p>如果您有意向成为志愿者，请联系我们获取更多信息。</p>
            </el-tab-pane>
          </el-tabs>
        </div>
      </div>
      
      <!-- 爱心榜 -->
      <div class="donation-card">
        <div class="donation-card-title">
          <i class="el-icon-trophy"></i>
          <span>爱心榜</span>
        </div>
        
        <div class="donor-list">
          <el-table
            :data="donorList"
            style="width: 100%"
            :header-cell-style="{background:'#f5f7fa',color:'#606266'}"
            :row-class-name="tableRowClassName">
            <el-table-column label="捐赠者" width="180">
              <template slot-scope="scope">
                <div style="display: flex; align-items: center;">
                  <el-avatar :size="40" icon="el-icon-user"></el-avatar>
                  <span style="margin-left: 10px">{{ scope.row.name }}</span>
                </div>
              </template>
            </el-table-column>
            <el-table-column prop="type" label="捐赠类型" width="120"></el-table-column>
            <el-table-column prop="amount" label="捐赠金额/物资" width="150">
              <template slot-scope="scope">
                <span v-if="scope.row.type === '现金捐赠'" style="color: #F56C6C; font-weight: bold;">¥{{ scope.row.amount }}</span>
                <span v-else>{{ scope.row.amount }}</span>
              </template>
            </el-table-column>
            <el-table-column prop="date" label="捐赠日期" width="120"></el-table-column>
          </el-table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Donation",
  data() {
    return {
      activeName: 'cash',
      donorList: [
        {
          name: '李先生',
          type: '现金捐赠',
          amount: '1000',
          date: '2023-03-15'
        },
        {
          name: '王女士',
          type: '物资捐赠',
          amount: '狗粮50kg',
          date: '2023-03-10'
        },
        {
          name: '张先生',
          type: '现金捐赠',
          amount: '500',
          date: '2023-03-05'
        },
        {
          name: '刘女士',
          type: '物资捐赠',
          amount: '猫砂20包',
          date: '2023-02'
        }
      ]
    }
  },
  methods: {
    tableRowClassName({row, rowIndex}) {
      return '';
    }
  }
}
</script>

.donation-container {
  min-height: calc(100vh - 60px);
  padding: 20px;
  background-color: #f5f7fa;
  max-width: 1200px;
  margin: 0 auto;
}

.donation-header {
  display: flex;
  align-items: center;
  margin-bottom: 30px;
  position: relative;
  padding-bottom: 15px;
}

.donation-header i {
  font-size: 28px;
  margin-right: 10px;
  color: #E6A23C;
}

.donation-header span {
  font-size: 24px;
  font-weight: 600;
  color: #E6A23C;
}

.header-divider {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100px;
  height: 3px;
  background: linear-gradient(to right, #E6A23C, #F56C6C);
  border-radius: 3px;
}

.donation-content {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
  gap: 30px;
  margin-top: 20px;
}

.donation-card {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  padding: 20px;
  transition: all 0.3s;
}

.donation-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
}

.donation-card-title {
  font-size: 18px;
  font-weight: 600;
  color: #303133;
  margin-bottom: 20px;
  display: flex;
  align-items: center;
}

.donation-card-title i {
  margin-right: 10px;
  color: #E6A23C;
  font-size: 20px;
}

.donation-card-content {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.donation-items {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
  gap: 20px;
}

.donation-item {
  text-align: center;
  width: 100px;
}

.donation-item-icon {
  width: 50px;
  height: 50px;
  margin: 0 auto 10px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.donation-item-name {
  font-size: 14px;
  color: #606266;
  margin-bottom: 5px;
}

.donation-item-desc {
  font-size: 12px;
  color: #909399;
}

.donation-methods {
  margin-top: 20px;
}

.donation-methods-title {
  font-size: 18px;
  font-weight: 600;
  color: #303133;
  margin-bottom: 20px;
  display: flex;
  align-items: center;
}

.donation-methods-title i {
  margin-right: 10px;
  color: #E6A23C;
  font-size: 20px;
}

.donation-tabs {
  margin-top: 20px;
}

.payment-methods {
  display: flex;
  justify-content: space-around;
  margin-top: 20px;
  flex-wrap: wrap;
  gap: 30px;
}

.payment-method {
  text-align: center;
  width: 200px;
  cursor: pointer;
  transition: all 0.3s;
}

.payment-method:hover {
  transform: scale(1.05);
}

.payment-method img {
  width: 100%;
  height: auto;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.payment-method-name {
  margin-top: 10px;
  font-size: 16px;
  color: #606266;
}

.contact-info {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
  margin-top: 20px;
}

.contact-item {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}

.contact-item i {
  font-size: 18px;
  color: #E6A23C;
  margin-right: 10px;
  width: 20px;
  text-align: center;
}

.contact-item span {
  font-size: 14px;
  color: #606266;
}

.donor-list {
  margin-top: 30px;
}

.donor-list-title {
  font-size: 18px;
  font-weight: 600;
  color: #303133;
  margin-bottom: 20px;
  display: flex;
  align-items: center;
}

.donor-list-title i {
  margin-right: 10px;
  color: #E6A23C;
  font-size: 20px;
}

.donor-table {
  width: 100%;
  border-collapse: collapse;
}

.donor-table th, .donor-table td {
  padding: 12px 15px;
  text-align: left;
  border-bottom: 1px solid #ebeef5;
}

.donor-table th {
  font-weight: 600;
  color: #606266;
  background-color: #f5f7fa;
}

.donor-table tr:hover {
  background-color: #f5f7fa;
}

.donor-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  margin-right: 10px;
  vertical-align: middle;
}

.donor-name {
  font-weight: 600;
  color: #303133;
}

.donor-amount {
  font-weight: 600;
  color: #F56C6C;
}

.donor-date {
  color: #909399;
  font-size: 12px;
}

.donor-message {
  color: #606266;
  max-width: 300px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

@media (max-width: 768px) {
  .donation-content {
    grid-template-columns: 1fr;
  }
  
  .payment-methods {
    flex-direction: column;
    align-items: center;
  }
  
  .payment-method {
    width: 80%;
    max-width: 300px;
  }
}

.payment-qrcode {
  width: 200px;
  height: 200px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  margin-bottom: 10px;
  padding: 20px;
  text-align: center;
}

.payment-qrcode div {
  margin-top: 10px;
  font-size: 14px;
  color: #606266;
}

.wechat-qrcode {
  border: 1px solid #07C160;
}

.alipay-qrcode {
  border: 1px solid #1677FF;
} 