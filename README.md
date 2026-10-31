# 小爱听书 · XiaoAiBooks

**本地自托管的听书项目，仅用于技术探索。作者不提供、托管或分发任何媒体源、书源订阅、音视频版权文件或付费资源。**

在自己的 NAS 上部署，管理自己有权使用的资源，将听书或 Navidrome 音乐投送至家中的小爱音箱，也可以在浏览器收听。“本地”指服务由自己部署；用户导入的在线书源按需解析和流式转发，不在 NAS 下载或保存有声书文件。

本仓库提供部署文件和版本记录，镜像通过 GitHub Container Registry 分发，不包含开发工作区或任何用户的账号、书源和配置。

## 功能

- Sunny UI 管理台：设备与连接、书源管理、节目准备、本地音乐、用户管理、播放与交互、其他设置。
- 用户自行导入书源，搜索并选定版本、章节和主播，为节目设置共享的唯一关键字与封面。
- 普通用户凭管理员注册码注册；节目共享，每个账号独立保存收听进度。
- 小米账号连接、音箱发现与绑定、播放、暂停、续播和持久断点。
- 已准备的听书节目可自动生成“小爱训练”的完整口令，固定回复“好”；支持明确序数的“第2季／第二季”读法。
- Navidrome 专辑、歌曲、歌手、封面与歌词；可在网页或已授权音箱收听。
- 企业微信独立自建应用：管理员搜索与编号选择节目，接收播放、暂停和自然结束通知，转发简单算术验证任务。

## Docker Compose 部署

需要 Docker Engine、Docker Compose v2，以及可连接所用小爱音箱的家庭网络。实际提供的架构、镜像身份和本地验证内容见[对应版本发布页](https://github.com/xudong7587/xiaoaibooks/releases)：本版提供 `linux/amd64` 与 `linux/arm64`。`RELEASE-MANIFEST.json` 是本地镜像清单，`REGISTRY-MANIFEST.json` 记录成功推送后的 GHCR 摘要；发布附件另提供 SHA256 校验文件。

```sh
mkdir xiaoaibooks
cd xiaoaibooks
curl -fLO https://raw.githubusercontent.com/xudong7587/xiaoaibooks/main/compose.yaml
curl -fLO https://raw.githubusercontent.com/xudong7587/xiaoaibooks/main/.env.example
cp .env.example .env
```

如果从 Release 附件下载，`default.env.example` 与仓库 `.env.example` 内容相同，将它复制为 `.env` 即可。

编辑 `.env`：

1. 用 `openssl rand -hex 32` 生成随机管理员密码，填入 `P0_ADMIN_TOKEN`，不要保留空值或示例密码。
2. 将 `PUBLISH_IP` 改为 NAS 的真实局域网 IP；保留 `127.0.0.1` 时只能在 Docker 主机本机访问。
3. 按需修改 `PUBLISH_PORT`，默认 `18765`。妥善保管 `.env`，建议权限 `600`。

```sh
docker compose up -d
docker compose ps
```

打开 `http://NAS局域网IP:18765/admin`，使用 `.env` 中的初始管理员密码登录。网页内修改密码后，以新密码登录。普通用户入口为 `/play`。

Compose 默认使用 Docker 命名卷保存私有数据，升级容器不会清空进度。需要绑定 NAS 目录时，将 `data:/data` 改为自己的目录，并确保容器用户 `10001:10001` 可以读写；不要将整个 NAS 或其他应用的数据目录挂载进容器。

## Docker 命令部署

下面与 Compose 使用同一镜像；请将管理员密码和端口绑定 IP 改为自己的值。

```sh
docker run -d \
  --name xiaoaibooks \
  --restart unless-stopped \
  --read-only --tmpfs /tmp:size=256m,mode=1777 --shm-size=128m \
  --cap-drop ALL --security-opt no-new-privileges:true \
  --memory 1536m --cpus 2 \
  --stop-timeout 90 \
  -p NAS局域网IP:18765:18765 \
  -e P0_ADMIN_TOKEN='替换为自己生成的随机密码' \
  -e APP_DATA_DIR=/data \
  -e XIAOMI_TOKEN_CACHE=/data/xiaomi-token.json \
  -v xiaoaibooks-data:/data \
  ghcr.io/xudong7587/xiaoaibooks:0.2.0
```

## 首次设置

1. **设备与连接**：登录自己的小米账号，完成小米要求的官方身份验证，发现并设置音箱房间、内网地址和默认进度账号。
2. **书源管理**：首次安装没有书源。自行导入有权使用的插件包，选择启用项。项目不提供书源下载地址。
3. **节目准备**：搜索、选择具体版本与章节，设置不重复的关键字，例如“睡前三国”。
4. **播放与交互**：先通过网页确认资源可播放，再开启对应房间的语音监听。管理员可设置多个启动词，用逗号分隔。
5. **节目训练自动同步**：连接已保存的小米账号的独立训练会话，再启用同步。准备好的听书节目会组合启动词和关键字创建私有训练；页面显示实际同步结果。不会自动发布训练，也不覆盖无关的人工训练。
6. **用户管理**：创建注册码，普通用户在 `/play` 注册；各账号从自己的断点续听。

先说“小爱同学”唤醒，再说“本地听书 睡前三国”。有进度时继续收听；暂停与继续可以直接说“暂停”“继续”，无需添加“本地”。音箱语音采用该设备设置的默认账号。

## 本地音乐与限制

在“本地音乐”连接自己的 Navidrome，填写地址、用户名和密码。音乐不经书源系统获取；无损音乐可请求 Navidrome 转为 MP3，需 Navidrome 已配置 FFmpeg 和转码规则。

“本地听歌 歌名”可搜索 Navidrome。**任意歌名开播前的原生“不支持本地音乐”等回复尚未完全解决。** 完整句训练的效果已在实机验证；只训练“本地听歌”不能据此实现任意歌名通配，节目训练不会为整个音乐库逐首建训练。

当前小米语音交互通过云端识别记录轮询，并非固件内拦截。没有对所有型号、固件或网络条件作兼容保证。L15A 与 X08E 有实机验证；X08E 使用触屏兼容投放，音箱屏幕可能显示厂商占位曲目，网页和通知显示实际资源。

音箱位置与媒体取流核对通过后才保存有效播放状态。网络中断、资源过期和固件差异可能影响定位；暂停后再升级可减少断点损失。容器重启不会自行开播。

## 企业微信（管理员专用）

使用独立自建应用，并在企业微信后台把应用可见范围限制为管理员。在“其他设置 → 企业微信交互”保存企业 ID、AgentId、Secret、Token、EncodingAESKey，启用接收消息，设置 HTTPS 回调 `/api/wecom/callback`。

启用管理员专用模式后，无需绑定普通用户或输入绑定码。配置明确的管理员 UserId 名单时，只处理名单成员；使用从应用识别管理员的模式时，先由管理员向应用发送一条消息，项目记录该签名有效的应用成员。真实出站通知不使用 `@all` 广播。

向应用发送“三体”或“准备 三体”，收到编号候选后，在有效期内回复“编号 关键字”，例如“2 睡前三体”。准备资源不会播放。搜索与回复状态显示在管理页；出网 IP 等企业微信平台限制仍需按官方要求配置。

播放、继续、暂停及自然结束通知由管理员按需启用，只发送给管理员名单。自然结束需要实际观测到终点，失联或重启不会冒充结束。

## 外网、更新与备份

需要外网网页或企微回调时，自行配置 HTTPS 反向代理，保留原始 Host，并在管理台填写公网服务地址。公网管理默认关闭，可按需要启用；仍需管理员登录。异地音箱入口暂时隐藏。

镜像使用固定版本标签。更新到新版本时，先将 `compose.yaml` 中 `image:` 的版本改为目标已发布版本，核对该版本清单，再执行：

```sh
docker compose pull
docker compose up -d
```

更新前备份私有数据卷；数据库使用 SQLite，建议停容器后备份卷，或使用 SQLite 在线备份。不要把账号、会话、Cookie、私有书源或备份提交到 GitHub。`docker compose down` 保留卷；`down -v` 会删除卷和数据。

## 许可与源码说明

第三方许可证及说明位于镜像 `/app/licenses`。前端包含 GPL-3.0-only 组件，镜像附带与该版本对应的完整前端修改源码和构建说明，不把压缩后的 JavaScript 当作源码。

```sh
docker compose cp app:/app/licenses/frontend-source.tar.gz ./frontend-source.tar.gz
```

以上命令用于 Compose 部署；使用前述 `docker run --name xiaoaibooks` 时，改用 `docker cp xiaoaibooks:/app/licenses/frontend-source.tar.gz ./frontend-source.tar.gz`。无需启动服务也可用 `docker create` 创建临时容器后提取，具体步骤见镜像 `/app/licenses/BUILDING-FRONTEND.md`。镜像包含运行所需的 Python 等程序文件，可以被提取；本仓库精简不等于镜像内代码不可查看。请阅读 `THIRD_PARTY_NOTICES.md` 及镜像内许可证。

## 免责声明

本项目仅用于本地听书、媒体管理与协议互通的技术探索，不提供媒体资源、书源订阅、版权文件、付费内容或任何形式的破解服务，也不授予用户获取第三方内容的权利。用户应自行确认所接入资源的合法来源、访问授权及适用服务条款，不得用于侵犯著作权、绕过付费或访问限制等用途。

项目按现状提供，不保证第三方网站、小米或企业微信接口持续可用。作者与小米、企业微信及任何媒体平台无隶属、授权或背书关系。请在自己有权管理的设备与账号上使用，并自行保护账号与私有数据。第三方组件的权利、义务和许可条件不受本段说明替代。
