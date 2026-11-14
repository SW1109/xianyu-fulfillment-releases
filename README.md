# 光年闲鱼助手

这里提供正式安装包、版本说明和 SHA-256 校验文件。业务源码独立维护，安装和检查公开更新不需要 GitHub 账号或访问令牌。

下载入口：https://github.com/SW1109/xianyu-fulfillment-releases/releases

## 支持环境

- Linux x86_64、Docker Engine、Docker Compose v2、Python 3、systemd、OpenSSL、curl、flock。
- 使用 SQLite 和固定浏览器运行时。其他架构、Windows/macOS 原生安装及外置数据库暂不支持本升级流程。
- 需要为应用、浏览器、备份和升级暂存文件预留内存及磁盘空间。

## 首次安装

1. 下载同一版本的安装包和同名 `.sha256` 文件，在所在目录运行 `sha256sum --check <安装包文件名>.sha256`。
2. 准备浏览器运行时：

   ```bash
   docker pull ghcr.io/christ9038/ydisks-xianyu-helper:1.0.14@sha256:4bc26ec0e0a2515ef65223be998bb26e4736103db25ce157a9593af51f9f5081
   docker tag ghcr.io/christ9038/ydisks-xianyu-helper:1.0.14@sha256:4bc26ec0e0a2515ef65223be998bb26e4736103db25ce157a9593af51f9f5081 guangnian-xianyu-browser:1.0.14
   ```

3. 将安装包中的完整目录内容放到 `/opt/guangnian-xianyu`，目录由 root 持有，权限设为700，然后运行 `bash install.sh`。
4. 首次安装会生成管理员密码和固定数据密钥，保存在权限600的 `.env` 中。请在服务器本地查看管理员密码；不要分享 `.env`，不要重新生成已经使用的数据密钥。
5. 按包内 `nginx.conf` 配置自己的域名、证书与 HTTPS 反向代理。默认应用仅监听本机 `127.0.0.1:59189`。

已有实例首次接入网页升级时，先完成备份，并保留原有 `.env`、`data/`、`browser_data/` 和旧镜像。安装新程序及升级组件后，再核对版本和原有数据。安装包不包含运行数据或真实凭据。

## 网页升级

系统管理员登录后，点击左下角版本提示打开“版本管理”，查看仓库更新说明、检查并确认升级，或选择历史版本回退。手机端入口在顶部导航旁。超过六小时未检查时自动查询一次发布源，有新版本只显示提示，不自动安装。下载校验、备份、切换和健康检查由独立升级组件执行；关闭浮层或网页暂时断开不会重复提交安装。

界面和发布标题使用 `v1.1.1` 这样的三段版本号，后续按三段版本递增。旧版实例仍从原“系统设置 → 版本更新”入口升级；安装包身份保留旧升级协议要求的兼容后缀，避免旧版无法识别，校验摘要仍绑定完整包名。

默认更新源为 `SW1109/xianyu-fulfillment-releases`，无需填写 `UPDATE_GITHUB_TOKEN`。发布权限只由维护者持有，不分发给安装实例。

如需使用自己的更新仓库，可在 `update.env` 中修改 `UPDATE_REPOSITORY`。只有使用私有更新源时才需要配置限定到该仓库的只读令牌，配置后重启 `guangnian-xianyu-update.service`。

## 备份与回退

完整备份位于 `backups/`，升级组件保留最近三次升级前的应用快照。回退前会验证文件完整性和数据库兼容性；数据库结构变化时，页面会说明原因并禁止直接回退。

回退仅恢复应用与前端，保留当前业务数据。启动失败时会尝试恢复原应用；如果旧版本不兼容当前数据，则保留备份并要求人工处理。不要直接覆盖已有数据库。

查看组件状态可使用 `systemctl status guangnian-xianyu-update.service`；排查日志可使用 `journalctl -u guangnian-xianyu-update.service`。向他人提供日志前应移除凭据和业务内容。

## 来源与许可

光年闲鱼助手基于 Christ9038/Ydisks-Xianyu-Helper v1.0.14 修改并维护，原始项目：https://github.com/Christ9038/Ydisks-Xianyu-Helper

安装包保留 Apache-2.0 `LICENSE` 与 `NOTICE`。浏览器运行时及其他依赖继续适用各自许可。
