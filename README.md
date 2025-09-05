# 🎬 网盘资源自动化管理助手 (tgto123)

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.13%2B-blue?style=for-the-badge&logo=python" alt="Python Version">
  <img src="https://img.shields.io/badge/Docker-Ready-blue?style=for-the-badge&logo=docker" alt="Docker Ready">
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" alt="License">
  <img src="https://img.shields.io/badge/Platform-Linux%20%7C%20macOS%20%7C%20Windows-lightgrey?style=for-the-badge" alt="Platform">
</p>

<p align="center">
  <strong>一个功能强大的网盘资源自动化管理助手，支持全自动推送每日热更剧集进123网盘，支持全自动监控转存123、115、天翼云盘tg频道分享链接</strong>
</p>

---

## 🌟 项目特色

### ✨ **核心功能**
- 🚀 **全自动转存多网盘TG频道资源** - 自动监控Telegram频道（例如 https://t.me/cookie_gy） ，自动转存123网盘、115网盘、天翼云盘等资源
- 🔗 **一键转存分享链接** - 直接转发123、115、天翼云盘分享链接即可自动转存（支持带图片的频道分享）
- 📤 **快速创建分享并发帖** - 通过Telegram机器人快速创建123网盘分享链接，并自动发布到123资源社区论坛
- 💾 **秒传功能支持** - 支持123网盘秒传JSON和秒传链接的直接转存，高效分享大文件
- ⚡ **磁力链离线下载** - 支持123网盘批量离线磁力链，直接发送磁力链即可处理
- 📁 **PT下载文件秒传** - 新增PT下载本地文件无限尝试秒传123或115网盘功能，避免运营商制裁
- 🔍 **智能搜索分享** - 使用/share关键词搜索123网盘文件夹，例如：/share 权力的游戏
- 🔄 **后台定时执行** - 123、115、天翼等频道监控转存在后台定时执行，无需人工干预
- 🔗 **直链生成服务** - 访问 http://127.0.0.1:12366/d/path 即可获取123文件下载直链

### 🏗️ **技术架构**
- **后端**: Python + Flask + SQLAlchemy
- **数据库**: SQLite
- **部署**: Docker + Docker Compose
- **API集成**: 123网盘API、115网盘API、天翼云盘API
- **Bot框架**: pyTelegramBotAPI

## 🐳 Docker 快速部署 (推荐)

### 📋 系统要求
- Docker 20.10+
- Docker Compose 2.0+
- 可访问Telegram的网络环境（需配置代理）
- 123网盘、115网盘、天翼云盘账号等

### 🚀 Docker Compose部署
完成docker部署后，需在 **NasIP:12366**（例如：`192.168.1.1:12366`）的配置页面中完成功能配置，否则无法正常使用。

### 📄 Docker Compose 配置示例

```yaml
version: '3'

services:
  tgto123-service:
    image: walkingd/tgto123:latest
    container_name: tgto123
    network_mode: host
    ports:
      - '12366:12366'
    environment:
      # 必填：代理配置（v2填20172带分流规则的端口，clash填7890端口）
      - HTTP_PROXY=http://127.0.0.1:7890
      # 必填：代理配置（v2填20172带分流规则的端口，clash填7890端口）
      - HTTPS_PROXY=http://127.0.0.1:7890
      # 必填：时区配置
      - TZ=Asia/Shanghai
      # 必填：TG机器人Token（从@BotFather获取）
      - ENV_TG_BOT_TOKEN=
      # 必填：TG管理员用户ID（从@userinfobot获取）
      - ENV_TG_ADMIN_USER_ID=
      # 必填：WEB配置页面的登录账号
      - ENV_WEB_PASSPORT=
      # 必填：WEB配置页面的登录密码
      - ENV_WEB_PASSWORD=
    volumes:
      # 持久化存储db目录
      - ./db:/app/db
      # 本地PT文件下载目录（左侧）映射到容器内监控目录（右侧）
      - /vol3/1000/Video/MoviePilot/transfer:/app/upload
      # 超过重试次数后将通过CD2上传文件
      - /vol1/1000/CloudNAS/CloudDrive/115云盘/Video/待归档影视/MP待归档影视:/app/transfer
    restart: always  # 容器退出后自动重启
```

### 📁 数据持久化

容器会将以下目录映射到主机：
- `./db` - 数据库文件和配置信息
- `./db/logs` - 应用日志文件
- `./upload` - 本地PT文件下载目录
- `./transfer` - 超过重试次数后将通过CD2上传文件

## 💻 使用指南

### 🎯 基础功能使用

#### 创建123分享链接
- 发送命令 `/share 搜索关键词`，例如：`/share 权力的游戏`
- 选择搜索结果中的文件夹，自动创建分享链接
- 可选择是否发布到123资源社区论坛

#### 转存123、115、天翼分享链接
- 直接转发123网盘、115网盘、天翼云盘的分享链接
- 支持带图片的频道分享消息直接转发
- 系统会自动识别并转存到指定目录

#### 转存123秒传内容
- 直接转发秒传JSON文件
- 直接发送秒传链接
- 系统会自动解析并尝试秒传

#### 提交123磁力链离线下载
- 直接发送磁力链接到机器人
- 系统会自动添加到123网盘离线任务列表

#### 获取123文件下载直链
- 访问 `http://127.0.0.1:12366/d/path`，其中`path`为完整的文件路径
- 例如：`http://127.0.0.1:12366/d/123云盘/Video/通用格式影视库/电视节目/日韩剧集/2025/红豆面包 (2025) {tmdb-262339}/Season 1/红豆面包.2025.S01E01.第1集.1080p.H.264.30fps.mkv`
- 系统会返回对应的文件下载直链

### 🔧 频道监控设置

1. **添加监控频道**
   - 在 ip:12366 后台配置页面配置
   - 按照提示输入频道ID和关键词过滤规则
   - 设置转存目标目录

2. **调整监控参数**
   - 可配置检查频率、过滤关键词、排除关键词等
   - 支持设置最大转存文件大小和类型限制

### 📋 PT下载文件秒传功能

1. **配置文件映射**
   - 在Docker Compose中配置PT下载目录映射

2. **启动秒传任务**
   - 系统会自动扫描映射目录中的文件
   - 尝试将文件秒传到123网盘或115网盘
   - 支持大文件分片上传和断点续传

3. **查看秒传进度**
   - 成功后系统会自动像TG机器人发送通知

## 🐛 故障排除

### 🔧 常见问题

#### Bot无响应
1. 检查Docker容器是否正常运行：`docker-compose ps`
2. 查看容器日志：`docker-compose logs -f`
3. 确认Telegram Bot Token配置正确

#### 转存失败
1. 检查网盘账号是否登录状态正常
2. 确认目标目录权限正确
3. 查看日志中的具体错误信息

#### 直链服务无法访问
1. 检查端口12366是否被占用：`netstat -tlnp | grep 12366`
2. 确认防火墙设置是否允许该端口访问

### 📋 日志查看
db/log目录中查看日志文件

## 🚀 版本更新

### 更新Docker镜像
发送/start 命令给机器人可查看当前的最新版本
```bash
# 拉取最新镜像
docker-compose pull

# 重启服务应用更新
docker-compose up -d
```

### 数据备份
```bash
# 备份数据目录
tar -czf backup-$(date +%Y%m%d).tar.gz db/ logs/

# 恢复数据
tar -xzf backup-20240101.tar.gz
```

### 技术栈致谢
- [pyTelegramBotAPI](https://github.com/eternnoir/pyTelegramBotAPI) - Telegram Bot框架
- [p123client](https://github.com/ChenyangGao/p123client) - 123网盘Python客户端
- [p115client](https://github.com/ChenyangGao/p115client) - 115网盘Python客户端
- [Flask](https://flask.palletsprojects.com/) - Web框架


<p align="center">
  <strong>🎉 享受高效便捷的网盘资源管理体验！</strong>
</p>

<p align="center">
  如果这个项目对您有帮助，请给个 ⭐ Star 支持一下！
</p>
