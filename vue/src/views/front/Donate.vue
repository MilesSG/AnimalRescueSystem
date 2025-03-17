<template>
  <div class="donate-container">
    <div class="donate-header">
      <i class="el-icon-money"></i>
      <span>爱心捐赠</span>
      <div class="header-divider"></div>
    </div>
    
    <div class="donate-content">
      <el-row :gutter="20">
        <el-col :span="16">
          <div class="donate-info-card">
            <div class="card-title">
              <i class="el-icon-info"></i>
              <span>关于捐赠</span>
            </div>
            <div class="card-content">
              <p>您的每一份爱心都将用于改善流浪动物的生活条件，提供必要的医疗救助和日常照料。我们承诺将每一笔捐款都用于动物救助事业，并定期公布善款使用情况。</p>
              <p>我们目前急需的物资包括：</p>
              <el-row :gutter="20" class="donate-items">
                <el-col :span="8">
                  <div class="donate-item">
                    <i class="el-icon-food"></i>
                    <div>
                      <h4>宠物食品</h4>
                      <p>猫粮、狗粮、幼犬/幼猫奶粉</p>
                    </div>
                  </div>
                </el-col>
                <el-col :span="8">
                  <div class="donate-item">
                    <i class="el-icon-first-aid-kit"></i>
                    <div>
                      <h4>医疗用品</h4>
                      <p>消毒液、绷带、药品等</p>
                    </div>
                  </div>
                </el-col>
                <el-col :span="8">
                  <div class="donate-item">
                    <i class="el-icon-house"></i>
                    <div>
                      <h4>生活用品</h4>
                      <p>毛毯、猫砂、宠物窝等</p>
                    </div>
                  </div>
                </el-col>
              </el-row>
            </div>
          </div>
          
          <div class="donate-ways-card">
            <div class="card-title">
              <i class="el-icon-connection"></i>
              <span>捐赠方式</span>
            </div>
            <div class="card-content">
              <el-tabs v-model="activeTab">
                <el-tab-pane label="现金捐赠" name="cash">
                  <div class="tab-content">
                    <p>您可以通过以下方式进行现金捐赠：</p>
                    <div class="payment-methods">
                      <div class="payment-method">
                        <div class="method-icon wechat-icon"></div>
                        <div class="method-qrcode">
                          <el-image 
                            style="width: 150px; height: 150px" 
                            src="https://img.yzcdn.cn/vant/cat.jpeg" 
                            fit="cover"
                            :preview-src-list="['https://img.yzcdn.cn/vant/cat.jpeg']">
                          </el-image>
                          <div>微信支付</div>
                        </div>
                      </div>
                      <div class="payment-method">
                        <div class="method-icon alipay-icon"></div>
                        <div class="method-qrcode">
                          <el-image 
                            style="width: 150px; height: 150px" 
                            src="https://img.yzcdn.cn/vant/dog.jpeg" 
                            fit="cover"
                            :preview-src-list="['https://img.yzcdn.cn/vant/dog.jpeg']">
                          </el-image>
                          <div>支付宝</div>
                        </div>
                      </div>
                    </div>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="物资捐赠" name="goods">
                  <div class="tab-content">
                    <p>您可以将物资邮寄至以下地址：</p>
                    <div class="address-info">
                      <p><i class="el-icon-location"></i> 收货地址：北京市海淀区中关村南大街5号</p>
                      <p><i class="el-icon-user"></i> 收件人：张三（动物救助中心）</p>
                      <p><i class="el-icon-phone"></i> 联系电话：010-65998877</p>
                    </div>
                    <div class="notice">
                      <p>* 请在包裹上注明"爱心捐赠"，并附上您的姓名和联系方式，以便我们向您表示感谢。</p>
                    </div>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="志愿服务" name="volunteer">
                  <div class="tab-content">
                    <p>我们非常欢迎您加入我们的志愿者团队，一起为流浪动物提供帮助：</p>
                    <div class="volunteer-info">
                      <div class="volunteer-item">
                        <i class="el-icon-time"></i>
                        <div>
                          <h4>日常照料</h4>
                          <p>帮助喂食、清洁、遛狗等日常工作</p>
                        </div>
                      </div>
                      <div class="volunteer-item">
                        <i class="el-icon-position"></i>
                        <div>
                          <h4>救助行动</h4>
                          <p>参与流浪动物的救助和转运</p>
                        </div>
                      </div>
                      <div class="volunteer-item">
                        <i class="el-icon-data-line"></i>
                        <div>
                          <h4>宣传推广</h4>
                          <p>帮助宣传动物保护理念和领养信息</p>
                        </div>
                      </div>
                    </div>
                    <el-button type="primary" class="volunteer-btn">申请成为志愿者</el-button>
                  </div>
                </el-tab-pane>
              </el-tabs>
            </div>
          </div>
        </el-col>
        
        <el-col :span="8">
          <div class="contact-card">
            <div class="card-title">
              <i class="el-icon-phone"></i>
              <span>联系方式</span>
            </div>
            <div class="contact-info">
              <div class="contact-item">
                <i class="el-icon-user"></i>
                <div>
                  <div class="label">负责人</div>
                  <div class="value">张三</div>
                </div>
              </div>
              <div class="contact-item">
                <i class="el-icon-phone-outline"></i>
                <div>
                  <div class="label">联系电话</div>
                  <div class="value">010-65998877</div>
                </div>
              </div>
              <div class="contact-item">
                <i class="el-icon-chat-dot-round"></i>
                <div>
                  <div class="label">微信</div>
                  <div class="value">xxxx-ss</div>
                </div>
              </div>
              <div class="contact-item">
                <i class="el-icon-message"></i>
                <div>
                  <div class="label">邮箱</div>
                  <div class="value">support@animalrescue.org</div>
                </div>
              </div>
              <div class="contact-item">
                <i class="el-icon-office-building"></i>
                <div>
                  <div class="label">办公地址</div>
                  <div class="value">北京市海淀区中关村南大街5号</div>
                </div>
              </div>
            </div>
          </div>
          
          <div class="donation-record-card">
            <div class="card-title">
              <i class="el-icon-medal"></i>
              <span>爱心榜</span>
            </div>
            <div class="donation-list">
              <div class="donation-item" v-for="(item, index) in donationList" :key="index">
                <el-avatar :size="40" :src="item.avatar">{{ item.name.substr(0, 1) }}</el-avatar>
                <div class="donation-info">
                  <div class="donor-name">{{ item.name }}</div>
                  <div class="donation-detail">
                    <span class="donation-type">{{ item.type }}</span>
                    <span class="donation-time">{{ item.time }}</span>
                  </div>
                </div>
                <div class="donation-amount" v-if="item.amount">{{ item.amount }}</div>
              </div>
            </div>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
export default {
  name: "Donate",
  data() {
    return {
      activeTab: 'cash',
      donationList: [
        {
          name: '李先生',
          avatar: 'https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png',
          type: '现金捐赠',
          amount: '¥1000',
          time: '2023-03-15'
        },
        {
          name: '王女士',
          avatar: 'https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png',
          type: '物资捐赠',
          amount: '狗粮50kg',
          time: '2023-03-10'
        },
        {
          name: '张先生',
          avatar: 'https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png',
          type: '现金捐赠',
          amount: '¥500',
          time: '2023-03-05'
        },
        {
          name: '刘女士',
          avatar: 'https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png',
          type: '物资捐赠',
          amount: '猫砂20袋',
          time: '2023-02-28'
        },
        {
          name: '赵先生',
          avatar: 'https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png',
          type: '现金捐赠',
          amount: '¥2000',
          time: '2023-02-20'
        }
      ]
    }
  },
  methods: {
    handleVolunteer() {
      this.$message.success('申请已提交，我们会尽快与您联系！')
    }
  }
}
</script>

<style scoped>
.donate-container {
  min-height: calc(100vh - 60px);
  padding: 20px;
  background-color: #f5f7fa;
}

.donate-header {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  position: relative;
  padding-bottom: 15px;
}

.donate-header i {
  font-size: 28px;
  margin-right: 10px;
  color: #E6A23C;
}

.donate-header span {
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

.donate-content {
  margin-top: 20px;
}

.donate-info-card, .donate-ways-card, .contact-card, .donation-record-card {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.05);
  padding: 20px;
  margin-bottom: 20px;
}

.card-title {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  padding-bottom: 10px;
  border-bottom: 1px solid #ebeef5;
}

.card-title i {
  font-size: 20px;
  margin-right: 8px;
  color: #E6A23C;
}

.card-title span {
  font-size: 18px;
  font-weight: 600;
  color: #303133;
}

.card-content {
  color: #606266;
  line-height: 1.6;
}

.card-content p {
  margin-bottom: 15px;
}

.donate-items {
  margin-top: 20px;
}

.donate-item {
  display: flex;
  align-items: center;
  padding: 15px;
  background-color: #f8f8f8;
  border-radius: 8px;
  height: 100%;
}

.donate-item i {
  font-size: 30px;
  margin-right: 15px;
  color: #E6A23C;
}

.donate-item h4 {
  margin: 0 0 5px 0;
  color: #303133;
}

.donate-item p {
  margin: 0;
  font-size: 13px;
  color: #909399;
}

.tab-content {
  padding: 15px 0;
}

.payment-methods {
  display: flex;
  justify-content: space-around;
  margin-top: 20px;
}

.payment-method {
  text-align: center;
}

.method-icon {
  width: 40px;
  height: 40px;
  margin: 0 auto 10px;
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center;
}

.wechat-icon {
  display: none;
  background-image: url('data:image/svg+xml;base64,PHN2ZyB0PSIxNjgzNzA0MjI4NTczIiBjbGFzcz0iaWNvbiIgdmlld0JveD0iMCAwIDEwMjQgMTAyNCIgdmVyc2lvbj0iMS4xIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHAtaWQ9IjI5NDEiIHdpZHRoPSIyMDAiIGhlaWdodD0iMjAwIj48cGF0aCBkPSJNMzM3LjEgMTMyLjJjLTE5LjggMC0zOS43IDAuOS01OS41IDIuNi0xMjQuNSAxMC44LTIyNS4yIDg4LjUtMjU0LjEgMTk2LjMtMTkuOCA3My41IDYuOSAxNTYuNiA3MS44IDIyMi4xIDQzLjIgNDMuNyA5NS44IDczLjUgMTUyLjcgODguNWwtMTkuOCA2MS4yYy0xLjcgNS4yLTAuOSAxMC44IDIuNiAxNS41IDMuNCA0LjMgOC42IDYuOSAxNC4yIDYuOSAxLjcgMCAzLjQgMCA1LjItMC45bDc3LjgtMzguOWMxMi45IDIuNiAyNS45IDMuNCozOC45IDQuMyAxOS44IDAuOSAzOS43IDAuOSA1OS41IDAuOSAxOS44IDAgMzkuNy0wLjkgNTkuNS0yLjYgMTI0LjUtMTAuOCAyMjUuMi04OC41IDI1NC4xLTE5Ni4zIDE5LjgtNzMuNS02LjktMTU2LjYtNzEuOC0yMjIuMS00OS4zLTQ5LjMtMTEyLjgtODEuMi0xODEuNS05MC4yLTUwLjEtNi45LTk5LjMtNi45LTE0OS40LTQ2LjN6IG0tMTI0LjUgMzI0LjRjLTE3LjIgMC0zMS4xLTE0LjItMzEuMS0zMS4xIDAtMTcuMiAxNC4yLTMxLjEgMzEuMS0zMS4xIDE3LjIgMCAzMS4xIDE0LjIgMzEuMSAzMS4xIDAgMTcuMi0xMy44IDMxLjEtMzEuMSAzMS4xeiBtMjQ4LjkgMGMtMTcuMiAwLTMxLjEtMTQuMi0zMS4xLTMxLjEgMC0xNy4yIDE0LjItMzEuMSAzMS4xLTMxLjEgMTcuMiAwIDMxLjEgMTQuMiAzMS4xIDMxLjEgMCAxNy4yLTEzLjggMzEuMS0zMS4xIDMxLjF6IiBmaWxsPSIjMDlCQjA3IiBwLWlkPSIyOTQyIj48L3BhdGg+PHBhdGggZD0iTTk5OS4zIDYxNy4yYy01NC40LTU0LjQtMTI5LjctODYuNy0yMDYuMy05NC45LTQzLjItNC4zLTg2LjctNC4zLTEzMC43LTAuOS0xNi40IDAuOS0zMi44IDIuNi00OS4zIDQuMy0xMDguNSA5LjUtMTk2LjMgNzYuOS0yMjEuMyAxNzEuNS0xNy4yIDY0LjEgNi4xIDEzNi42IDYyLjkgMTkzLjcgMzcuMiAzNy4yIDgzLjIgNjMuOCAxMzIuNCA3Ni45bC0xNy4yIDUzLjZjLTEuNyA1LjItMC45IDEwLjggMi42IDE1LjUgMy40IDQuMyA4LjYgNi45IDE0LjIgNi45IDEuNyAwIDMuNCAwIDUuMi0wLjlsNjcuNi0zMy43YzExLjIgMi42IDIyLjQgMy40IDMzLjcgMy40IDE3LjIgMC45IDM0LjYgMC45IDUxLjggMC45IDE3LjIgMCAzNC42LTAuOSA1MS44LTIuNiAxMDguNS05LjUgMTk2LjMtNzYuOSAyMjEuMy0xNzEuNSAxNy4yLTYzLjgtNi4xLTEzNi42LTYyLjktMTkzLjd6IG0tMzU5LjcgMjU3LjZjLTE1LjUgMC0yNy42LTEyLjEtMjcuNi0yNy42IDAtMTUuNSAxMi4xLTI3LjYgMjcuNi0yNy42IDE1LjUgMCAyNy42IDEyLjEgMjcuNiAyNy42IDAgMTUuNS0xMi4xIDI3LjYtMjcuNiAyNy42eiBtMjE2LjIgMGMtMTUuNSAwLTI3LjYtMTIuMS0yNy42LTI3LjYgMC0xNS41IDEyLjEtMjcuNiAyNy42LTI3LjYgMTUuNSAwIDI3LjYgMTIuMSAyNy42IDI3LjYgMCAxNS41LTEyLjEgMjcuNi0yNy42IDI3LjZ6IiBmaWxsPSIjMDlCQjA3IiBwLWlkPSIyOTQzIj48L3BhdGg+PC9zdmc+');
}

.alipay-icon {
  display: none;
  background-image: url('data:image/svg+xml;base64,PHN2ZyB0PSIxNjgzNzA0MjY4MjI1IiBjbGFzcz0iaWNvbiIgdmlld0JveD0iMCAwIDEwMjQgMTAyNCIgdmVyc2lvbj0iMS4xIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHAtaWQ9IjQwODgiIHdpZHRoPSIyMDAiIGhlaWdodD0iMjAwIj48cGF0aCBkPSJNMjMwLjQgNzk1LjJjLTE0LjQgMC0yNy4yLTEuNi0zOC40LTYuNGw1Ni04Mi40YzEwLjQgMi40IDIwLjggMy4yIDMwLjQgMy4yIDM0LjQgMCA0OC44LTIwLjggNTYtNDEuNmwtMTYwLTQxNS4yaDExMmw5NiAyODhoMC44bDk2LTI4OGgxMTJMMzQ2LjQgNzUyYy0yNCA2NC04MC44IDQzLjItMTE2IDQzLjJ6IiBmaWxsPSIjMDA5RkU5IiBwLWlkPSI0MDg5Ij48L3BhdGg+PHBhdGggZD0iTTY0Mi40IDc5MS4yYy0xMTIgMC0xOTItNTYtMTkyLTE1MiAwLTg4IDgwLTE0NCAxOTItMTQ0IDExMiAwIDE5MiA1NiAxOTIgMTQ0IDAgOTYtODAgMTUyLTE5MiAxNTJ6IG0wLTIyNGMtNjQgMC0xMTIgMzItMTEyIDcyIDAgNDAgNDggNzIgMTEyIDcyczExMi0zMiAxMTItNzJjMC00MC00OC03Mi0xMTItNzJ6IiBmaWxsPSIjMDA5RkU5IiBwLWlkPSI0MDkwIj48L3BhdGg+PHBhdGggZD0iTTk5MiA2NzIuOGMwLTggMC0xNi44LTAuOC0yNC44SDU0NHYtNzJoMjg4di00OEg1NDR2LTk2aDI0MHYtNDhINDk2di00OGgtOTZ2NDhIMjU2djQ4aDI0MHY5NkgyMDh2NDhoMjg4djcySDE5Mi44Yy0wLjggOC0wLjggMTYuOC0wLjggMjQuOCAwIDIwLjggMS42IDQwLjggNC44IDYwLjhoMzA0LjhjLTQwIDU2LTEwNCA4OC44LTE3Ni44IDEwNC0xNiAzLjItMzIgNS42LTQ4IDcuMmwzMiA4OGM4OC0xNiAxNjgtNTYgMjI0LTExMiA1NiA1NiAxMzYgOTYgMjI0IDExMmwzMi04OGMtMTYtMS42LTMyLTQtNDgtNy4yLTcyLjgtMTUuMi0xMzYuOC00OC0xNzYuOC0xMDRoMzA0LjhjMy4yLTIwIDQuOC00MCA0LjgtNjAuOHoiIGZpbGw9IiMwMDlGRTkiIHAtaWQ9IjQwOTEiPjwvcGF0aD48L3N2Zz4=');
}



.method-qrcode {
  margin-top: 10px;
  text-align: center;
}

.method-qrcode div {
  margin-top: 5px;
  color: #606266;
}

.address-info {
  background-color: #f8f8f8;
  padding: 15px;
  border-radius: 8px;
  margin: 15px 0;
}

.address-info p {
  margin: 10px 0;
}

.address-info i {
  margin-right: 5px;
  color: #E6A23C;
}

.notice {
  font-size: 13px;
  color: #909399;
  margin-top: 15px;
}

.volunteer-info {
  margin: 20px 0;
}

.volunteer-item {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
  padding: 15px;
  background-color: #f8f8f8;
  border-radius: 8px;
}

.volunteer-item i {
  font-size: 24px;
  margin-right: 15px;
  color: #E6A23C;
}

.volunteer-item h4 {
  margin: 0 0 5px 0;
  color: #303133;
}

.volunteer-item p {
  margin: 0;
  font-size: 13px;
  color: #909399;
}

.volunteer-btn {
  width: 100%;
  margin-top: 15px;
}

.contact-info {
  padding: 10px 0;
}

.contact-item {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.contact-item i {
  font-size: 24px;
  margin-right: 15px;
  color: #E6A23C;
  width: 24px;
  text-align: center;
}

.contact-item .label {
  font-size: 14px;
  color: #909399;
  margin-bottom: 5px;
}

.contact-item .value {
  font-size: 16px;
  color: #303133;
  font-weight: 500;
}

.donation-list {
  max-height: 400px;
  overflow-y: auto;
}

.donation-item {
  display: flex;
  align-items: center;
  padding: 15px 0;
  border-bottom: 1px solid #ebeef5;
}

.donation-item:last-child {
  border-bottom: none;
}

.donation-info {
  flex: 1;
  margin-left: 15px;
}

.donor-name {
  font-size: 16px;
  color: #303133;
  margin-bottom: 5px;
}

.donation-detail {
  font-size: 13px;
  color: #909399;
}

.donation-type {
  margin-right: 10px;
}

.donation-amount {
  font-size: 16px;
  color: #F56C6C;
  font-weight: 600;
}
</style>