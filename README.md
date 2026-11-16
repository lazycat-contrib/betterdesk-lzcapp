# BetterDesk — 懒猫微服打包（喵喵商店）

[BetterDesk](https://github.com/UNITRONIX/BetterDesk) 的懒猫微服 LPK 打包：开源自托管远程桌面平台，
RustDesk 协议服务端（Go，净室实现）+ Node.js Web 管理控制台，兼容标准 RustDesk 客户端。
上游 AGPL-3.0。

## 发布方式

- 仅发布到喵喵商店（私有/社区商店），不提交官方商店。
- **镜像模式**（`delivery.mode: mirror`）：构建时把 `ghcr.io/unitronix/betterdesk:<tag>`
  改写为 `ghcr.1ms.run/unitronix/betterdesk:<tag>` 加速直连上游，并校验 digest 一致。
- 版本跟随上游 GHCR 镜像 tag（`^[0-9]+\.[0-9]+\.[0-9]+$`）自动更新。

## 结构

- 单容器 all-in-one 官方镜像（Go 服务端 + Node.js 控制台 + supervisor）。
- Web 控制台（5000）走 HTTP upstream；RustDesk 客户端端口走 L4 ingress：
  TCP 21115/21116/21117/21118/21119/21121，UDP 21116。
- SQLite 单机模式（上游 quick-start 默认），数据持久化在 `/lzcapp/var/server` 与 `/lzcapp/var/console`。
- 管理员凭据由安装参数种子（首次启动），`/login` 自动填充实现免密登录。

## 自动发布

`.github/workflows/lazycat.yml` 每 3 小时 + 手动触发，调用
`ca-x/lazycat-github-action`（`operation: auto`），检测上游新版本 → 构建 LPK → 发布喵喵商店 →
GitHub Release 附版本化 LPK。

手动触发：

```bash
gh workflow run lazycat.yml -R lazycat-contrib/betterdesk-lzcapp
```

Secrets：`APPSTORE_URL` / `APPSTORE_TOKEN` 在 lazycat-contrib 组织级配置，仓库无需单独设置。

## 本地构建

```bash
lzc-cli project release -o betterdesk.lpk
```
