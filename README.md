# 🐾 流浪动物救助系统 (Animal Rescue System)

## 📋 项目介绍

这是一个基于SpringBoot+Vue的流浪动物救助管理系统，旨在帮助管理流浪动物的救助、领养、绝育等信息，提高救助效率和管理水平。

## 🧩 系统功能模块

### 前台功能

1. **首页展示** - 系统概况和最新动态
2. **宠物领养** - 流浪动物信息浏览与领养申请
3. **宠物百科** - 各类宠物饲养知识库
4. **科普文章** - 宠物健康、训练和保护知识
5. **宠物论坛** - 用户交流平台
6. **走失寻回** - 宠物走失登记和寻找服务
7. **爱心捐赠** - 捐赠渠道和历史记录
8. **个人中心** - 用户资料和申请管理

### 后台管理

1. **用户管理** - 账号权限管理
2. **动物管理** - 救助动物信息记录
3. **领养管理** - 领养申请审核和跟踪
4. **绝育管理** - 动物绝育信息记录
5. **救助管理** - 救助过程记录
6. **医疗管理** - 动物医疗信息记录
7. **捐赠管理** - 捐赠记录管理
8. **内容管理** - 文章和帖子管理
9. **数据统计** - 数据分析和统计
10. **系统设置** - 系统参数配置

## 🛠️ 技术栈

- **后端**: SpringBoot 2.5.9, MyBatis-Plus, MySQL
- **前端**: Vue 2.x, Element UI, Axios

## 🔧 环境要求

- JDK 1.8+
- Node.js (推荐v16.x)
- MySQL 5.7+
- Maven 3.6+

## 💾 数据库配置

```sql
CREATE DATABASE IF NOT EXISTS stray_animal DEFAULT CHARACTER SET utf8mb4;
USE stray_animal;
SOURCE stray_animal.sql;
```

## 🚀 快速部署指南

### 关键配置

修改`springboot/src/main/resources/application.yml`中的文件上传路径:
```yaml
files:
  upload:
    path: D:/YourPath/AnimalRescueSystem/springboot/files/  # 改为您的绝对路径
```

> ⚠️ **重要**: 文件路径必须是绝对路径且目录必须存在

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
- **默认账号/密码**: admin/admin

## ❓ 常见问题

### 图片显示问题

移植项目时，确保:
1. 配置正确的文件上传绝对路径
2. 确保文件目录已创建
3. 同时复制`files`目录下的所有文件

### 其他常见问题

- **Node.js版本问题**: v17+版本需使用`--openssl-legacy-provider`选项
- **端口占用**: 如遇端口占用，确保修改相应配置
- **Vue前端端口**: 前端可能运行在8080-8100等不同端口，这是正常现象

## 📁 项目结构

项目包含后端(springboot)、前端(vue)和数据库脚本(sql)三个主要部分。
