# 🐾 流浪动物救助系统 (Animal Rescue System)

## 📋 项目介绍

这是一个基于SpringBoot+Vue的流浪动物救助管理系统，旨在帮助管理流浪动物的救助、领养、绝育等信息，提高救助效率和管理水平。

## 🛠️ 技术栈

### 后端
- SpringBoot 2.5.9
- MyBatis-Plus
- MySQL

### 前端
- Vue 2.x
- Element UI
- Axios

## 🔧 环境要求

- JDK 1.8+
- Node.js (推荐使用v16.x，如使用更高版本需特殊配置)
- MySQL 5.7+
- Maven 3.6+

## 💾 数据库配置

1. 创建名为`stray_animal`的数据库
2. 导入项目根目录下的`stray_animal.sql`文件

```sql
-- 在MySQL中执行以下命令
CREATE DATABASE IF NOT EXISTS stray_animal DEFAULT CHARACTER SET utf8mb4;
USE stray_animal;
-- 然后导入SQL文件
SOURCE /path/to/stray_animal.sql;
```

## 🚀 启动步骤

### 1. 启动后端

```powershell
# 进入后端目录
cd springboot

# 编译打包（跳过测试）
mvn clean package -DskipTests

# 运行SpringBoot应用
java -jar target/springboot-0.0.1-SNAPSHOT.jar
```

### 2. 启动前端

```powershell
# 进入前端目录
cd vue

# 如果使用Node.js v17+版本，需要设置以下环境变量解决加密问题
$env:NODE_OPTIONS="--openssl-legacy-provider"

# 安装依赖（首次运行时需要）
npm install

# 启动开发服务器
npm run serve
```

## 🌐 访问地址

- 后端API: http://localhost:9090
- 前端界面: http://localhost:8080

## 👤 默认账号

- 管理员账号: **admin**
- 管理员密码: **admin**

> ⚠️ **重要提示**: 系统的默认登录账号和密码都是 **admin**

## ❓ 常见问题

### 1. 前端启动报错 `error:0308010C:digital envelope routines::unsupported`

**解决方案**: 这是因为Node.js v17+版本中的OpenSSL变更导致的，可以通过设置环境变量解决：

```powershell
$env:NODE_OPTIONS="--openssl-legacy-provider"
npm run serve
```

### 2. 后端报错 `无token，请重新登录`

**解决方案**: 这是正常现象，需要先在前端登录系统获取token。请使用账号密码 **admin/admin** 登录。

### 3. 图片无法显示 `文件下载失败，文件不存在`

**解决方案**: 检查`application.yml`中的文件上传路径配置是否正确：

```yaml
files:
  upload:
    path: D:\Thesis_Revision\Projects_Code\AnimalRescueSystem\springboot\files/
```

确保该目录存在且有读写权限。

## 📁 项目结构

```
AnimalRescueSystem/
├── springboot/           # 后端代码
│   ├── src/              # 源代码
│   ├── target/           # 编译输出
│   └── files/            # 上传文件存储
├── vue/                  # 前端代码
│   ├── src/              # 源代码
│   ├── public/           # 静态资源
│   └── node_modules/     # 依赖包
├── sql/                  # SQL脚本
├── 文档/                 # 项目文档
└── stray_animal.sql      # 数据库初始化脚本
```

## 📝 注意事项

- 确保MySQL服务已启动，且用户名密码与配置文件一致
- 确保后端启动成功后再启动前端
- 如遇到权限问题，请以管理员身份运行命令
- 首次运行前端项目时需要安装依赖（npm install）

## 🔄 更新日志

- 2025-03-16: 系统初始化完成 