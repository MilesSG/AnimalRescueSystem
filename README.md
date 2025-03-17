# 🐾 流浪动物救助系统 (Animal Rescue System)

## 📋 项目介绍

这是一个基于SpringBoot+Vue的流浪动物救助管理系统，旨在帮助管理流浪动物的救助、领养、绝育等信息，提高救助效率和管理水平。

## 🧩 系统功能模块

### 前台功能

1. **首页展示** - 展示系统概况和最新动态，包括救助流程、统计数据和最新领养信息。

2. **宠物领养** - 展示待领养的流浪动物信息，用户可以浏览详情并提交领养申请。支持按品种、年龄、性别等条件筛选。

3. **宠物百科** - 包含各类宠物饲养知识，分为犬类、猫类等分类，帮助主人更好地照顾宠物。

4. **科普文章** - 提供专业的宠物健康、行为训练、饲养知识和动物保护文章，支持在线阅读和发布。

5. **宠物论坛** - 用户交流平台，可发布帖子、评论回复，分享宠物饲养经验和故事。

6. **走失寻回** - 提供宠物走失登记和寻找服务，用户可发布走失宠物信息和目击线索。

7. **爱心捐赠** - 支持物资和资金捐赠，展示捐赠方式和用途，以及历史捐赠记录。

8. **个人中心** - 用户可管理个人资料、查看领养申请状态、发布的文章和帖子等。

### 后台管理

1. **用户管理** - 管理员可以查看、编辑、删除和禁用用户账号，设置权限等级。

2. **动物管理** - 记录救助动物的详细信息，包括品种、年龄、健康状况、救助地点、照片等。

3. **领养管理** - 审核领养申请，跟踪领养状态，回访记录，确保动物被妥善安置。

4. **绝育管理** - 记录动物绝育信息，包括手术时间、手术医院、费用等。

5. **救助管理** - 记录动物救助信息，救助人员、救助地点、救助过程和后续处理。

6. **医疗管理** - 记录动物医疗信息，包括疾病、治疗方案、用药记录、恢复情况等。

7. **捐赠管理** - 管理捐赠记录，包括捐赠人、捐赠物品/金额、用途分配等。

8. **内容管理** - 管理科普文章、论坛帖子，可进行审核、编辑和删除操作。

9. **数据统计** - 提供各类数据的统计分析，如救助数量、领养成功率、医疗费用等。

10. **系统设置** - 管理站点参数、权限配置、备份恢复等系统功能。

## 🛠️ 技术栈

- **后端**: SpringBoot 2.5.9, MyBatis-Plus, MySQL
- **前端**: Vue 2.x, Element UI, Axios

## 🔧 环境要求

- JDK 1.8+
- Node.js (推荐v16.x，更高版本需使用`--openssl-legacy-provider`选项)
- MySQL 5.7+
- Maven 3.6+

## 💾 数据库配置

```sql
CREATE DATABASE IF NOT EXISTS stray_animal DEFAULT CHARACTER SET utf8mb4;
USE stray_animal;
SOURCE /path/to/stray_animal.sql;
```

## 🚀 快速部署指南

### 关键配置

1. **后端配置** - `springboot/src/main/resources/application.yml`:
   ```yaml
   server:
     port: 9091  # 确保端口不冲突
   
   spring:
     datasource:
       driver-class-name: com.mysql.cj.jdbc.Driver
       url: jdbc:mysql://localhost:3306/stray_animal?serverTimezone=GMT%2b8
       username: root  # 你的数据库用户名
       password: 你的密码  # 你的数据库密码
   
   files:
     upload:
       path: D:/YourPath/AnimalRescueSystem/springboot/files/  # 文件存储的绝对路径
   ```
   > ⚠️ **重要**: 文件路径必须是绝对路径且目录必须存在。Windows系统请使用正斜杠(/)或双反斜杠(\\\\)。

2. **前端配置** - `vue/src/config.js`:
   ```javascript
   // 配置API和文件服务器地址
   export const API_URL = 'http://localhost:9091';
   export const FILE_URL = 'http://localhost:9091/file/';
   ```

### 启动服务

1. **启动后端**:
   ```
   cd springboot
   mvn clean package
   java -jar target/springboot-0.0.1-SNAPSHOT.jar
   ```

2. **启动前端**:
   ```powershell
   cd vue
   
   # Node.js v17+版本必须设置:
   $env:NODE_OPTIONS="--openssl-legacy-provider"  # Windows PowerShell
   # 或
   export NODE_OPTIONS="--openssl-legacy-provider"  # Linux/Mac
   
   npm install
   npm run serve
   ```

## 🌐 访问信息

- **访问地址**: http://localhost:8080
- **默认账号**: admin
- **默认密码**: admin

## ❓ 常见问题

### 图片显示相关问题

图片显示是本系统移植到其他电脑时最常见的问题。请确保:

1. **文件路径配置正确**
   - 确保`application.yml`中的`files.upload.path`设置为您当前环境的绝对路径
   - 确保该目录已创建且有读写权限

2. **端口配置一致**
   - 后端端口号(默认9091)必须与前端配置中的API地址一致
   - 如需更改端口号，必须同时修改:
     - 后端`application.yml`中的`server.port`
     - 前端`config.js`中的`API_URL`和`FILE_URL`

3. **数据库图片URL自动转换**
   - 系统内置自动URL转换功能，会将数据库中的旧URL转换为新URL
   - 如果访问"http://localhost:8080"和"http://localhost:8081"显示相同内容，这是正常的，因为Vue开发服务器会自动使用下一个可用端口

4. **确保文件已正确复制**
   - 移植系统时，确保`files`目录下的所有文件也一起复制到新环境

### 其他常见问题

- **Node.js版本问题**: v17+版本需使用`--openssl-legacy-provider`选项
- **端口占用**: 如遇端口占用，修改端口号并确保前后端配置一致
- **Vue模板错误**: 确保`v-else`对应有`v-if`条件，特别是图片上传组件

## 📁 项目结构

```
AnimalRescueSystem/
├── 📂 springboot/           # 后端项目
│   ├── 📂 src/              # 源代码
│   │   ├── 📂 controller/   # 控制器层，处理HTTP请求
│   │   ├── 📂 service/      # 服务层，业务逻辑
│   │   ├── 📂 mapper/       # 数据访问层
│   │   ├── 📂 entity/       # 实体类
│   │   ├── 📂 common/       # 通用工具和配置
│   │   └── 📂 exception/    # 异常处理
│   ├── 📂 files/            # 文件存储目录
│   └── 📄 pom.xml           # Maven配置
├── 📂 vue/                  # 前端项目
│   ├── 📂 src/              # 源代码
│   │   ├── 📂 views/        # 视图组件
│   │   │   ├── 📂 front/    # 前台页面
│   │   │   └── 📂 admin/    # 后台管理页面
│   │   ├── 📂 components/   # 可复用组件
│   │   ├── 📂 router/       # 路由配置
│   │   ├── 📂 assets/       # 静态资源
│   │   └── 📂 utils/        # 工具函数
│   ├── 📂 public/           # 静态资源
│   └── 📄 package.json      # NPM配置
├── 📂 sql/                  # SQL脚本
└── 📄 README.md             # 项目说明
```

## 📝 开发指南

### ✨ 添加新功能

1. 🔙 后端：在`springboot/src/main/java/com/qingge/springboot/controller`中添加新的控制器
2. 🔜 前端：在`vue/src/views`中添加新的视图组件
3. 🔀 路由：在`vue/src/router/index.js`中添加新的路由

### 🔄 修改数据库结构

1. 📝 更新`sql/stray_animal.sql`文件
2. 🏗️ 在`springboot/src/main/java/com/qingge/springboot/entity`中添加或修改实体类
3. 🔍 在`springboot/src/main/java/com/qingge/springboot/mapper`中添加或修改Mapper接口

## 📝 许可证

本项目采用 MIT 许可证 