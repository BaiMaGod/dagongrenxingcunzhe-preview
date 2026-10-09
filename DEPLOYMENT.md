# 打工人幸存者｜LayaAir 3.4 免费 GitHub Pages 预览发布

本仓库 **公开**，仅保留发布工作流、部署说明和 Pages 输出。所有游戏源码、资产及设计文档仍放在私有仓库 [dagongrenxingcunzhe](https://github.com/BaiMaGod/dagongrenxingcunzhe)。不得把 private checkout、源码、Node 依赖或 Token 作为 Artifact / Pages 内容上传。

沿用已经在 **baozhiguoyuan-preview** 实施的免费部署架构：由本公开库的 GitHub-hosted Runner 使用只读凭证检出私有正式源码，安装官方 LayaAir 3.4.0 CLI，构建 Web，只上传可运行的静态游戏文件给 GitHub Pages。

## 一次性仓库设置（用户操作）

1. 打开本公开仓库 **Settings → Secrets and variables → Actions → New repository secret**。
2. 添加 `DAGONGREN_SOURCE_READ_TOKEN`，值为 GitHub fine-grained PAT，仓库访问限定 **Only select repositories → BaiMaGod/dagongrenxingcunzhe**，权限 **Contents: Read-only**（Metadata Read 按 GitHub 默认）。不需写权限。
3. 打开 **Settings → Pages → Build and deployment → Source: GitHub Actions**。
4. 确认此**公开仓库** Actions 允许执行（设置位置 Settings → Actions）。
5. Secret 不要发在聊天里，也不要写进 README、代码或 Git 提交。

## 执行方式

打开 [Actions → LayaAir 3.4 免费构建与 Pages 发布](../../actions/workflows/build-laya-pages.yml) → Run workflow。

默认 `publish=false`：仅进行私有源码拉取、逻辑测试、Laya 构建与浏览器烟测，不发布。全部通过后，再次运行并选择 `publish=true` 才允许部署到 Pages。

当前研发测试源锁定在 `dev/m9-laya-build`，**M9 尚未完成正式浏览器游戏流程验收**。合入私有仓库主线后，需把工作流 private checkout ref 切回 `main`，并记录本次检出源码 SHA。

## 流程门禁

- 公开 Runner 检出私有源码时禁用 `persist-credentials`，不把只读 Token 保存到发布包。
- 执行 `npm run check` 和官方模板接线测试。
- 安装官方 CLI **3.4.0**；创建官方 2D 空工程；保留官方 `CompilerSettings.json` 和 `Main.ts.meta` UUID；只将 `Main.ts` 转发到现有 `Entry.main`。
- 运行真实 `layaair --version=3.4.0 build web`，要求输出静态 `index.html`、JS、数值配置。
- 运行 Chromium 桌面/手机竖屏启动烟测，截图可作为私有 CI 诊断结果。
- 仅当所有步骤成功且操作者选择 `publish=true` 时上传 Web 文件并调用 `actions/deploy-pages`。
- 部署完成后，用 Pages 上的 `build-manifest.json.commitSha` 与该次 private checkout SHA 校验，禁止把旧包当新版。

## 未验证项与限制

浏览器启动/Canvas 烟测 **不能代替**八分钟完整玩法测试；需要单独跑选择职业、移动、升级三选一、10/20 日商城、Boss、结算、放弃和返回下一月的 E2E 测试，并覆盖 Android/iOS 真机。

本项目的正式 Laya 工程尚在 M9 接入阶段；任何构建、CI、Pages 网址只有在检查成功后才能标为可试玩。

公开仓库通常可使用免费 GitHub-hosted runner 与 Pages，但具体配额仍以 GitHub 账户与官方政策为准。
