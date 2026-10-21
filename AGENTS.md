# AGENTS.md — mugsun-app

本文件写给在本仓工作的开发者和 AI 助手。内容以本仓代码为准；与代码不一致时以代码为准，并请顺手修正本文件。

## 仓库是什么

mugsun 平台的**移动端应用仓库**（规划对接 mugsun-boot，基于 uni-app x）。  
截至本文件所依据的提交，公开内容只有说明与许可证，**尚无可构建的应用源码**（无 `package.json` / `pages.json` / `manifest` / 业务页面）。

以根目录 `README.md` 的「当前状态」表为准；旧文档里的功能列表和「打开即可跑」步骤不得当作现状。

`CLAUDE.md` 是指向本文件的软链，只改 `AGENTS.md`。

## 源码落地前

- 不要编造页面结构、构建命令或联调步骤。
- 可改：`README.md` / `README_EN.md`、`CHANGELOG.md`、`CONTRIBUTING.md`、`LICENSE`，新建或修改 `.gitignore`，以及本文件。
- 中英文 README 同一次提交一起改；「当前状态」表的「公开内容」一行要与仓库实际文件一致。

## 密钥与配置（现在起就适用）

- 密钥和证书一律不入库，包括 uni-pay：`uni-config-center/uni-pay/` 下只提交内容为占位值的 `*.example.(js|ts|json)`。
- 源码首次提交时一并加入 `.gitignore`，忽略规则覆盖全仓、不限 uni-pay 路径：`*.p12`、`*.pem`、`*.p8`、`*.crt`、`*.key`、`*.pfx`、`*.cer`、`*.der`、`*.jks`、`*.keystore`、`*.p7b`，大小写不敏感（如写成 `*.[kK][eE][yY]`）。`uni-config-center/uni-pay/` 下另外忽略 `config.(js|ts|json)`。uniappx-ui 的 `.gitignore` 只覆盖 uni-pay，不能照抄。
- `manifest` 的 appid、`mp-weixin.appid`、各 SDK 的 appkey / secret 在仓内一律写占位值，真值放在仓外。
- 误提交了真实密钥：**先轮换**，再处理提交历史。
- 客户端不内置任何服务端密钥。

## 安全边界

- CORS 只约束浏览器，管不到 App 和小程序；接口安全靠服务端鉴权（只有 H5 请求带 Origin；App 的 uni.request 与微信小程序不受 CORS 约束）。
- `web-view` 和外链只允许白名单内的 `https` 地址，复用 uniappx-ui 的 URL 校验（拒绝反斜杠、控制字符、userinfo）；禁止用参数拼 URL 再打开。

## 依赖

- `mugsun-uniappx-ui` 和 `mugsun-uniappx-sdk` 是私有仓，不能用 npm 安装；以 `uni_modules` 方式引入。
- 引入或升级时在 `CHANGELOG.md` 记下来源仓和 commit SHA；升级单独一个提交。

## 构建与 CI

- HBuilderX/uni-app 构建只在指定构建机上跑（HBuilderX 5.26 CLI）。
- CI 已建最小 workflow（.github/workflows/ci.yml）：gitleaks 全历史、markdown 链接检查、osv-scanner 依赖扫描（无 lockfile 时输出「无依赖」并通过）；其余见「源码落地后」。

## 源码落地后（预期，待代码出现再核对）

以下条目在源码发布后按实际目录核对改写，发布前仅作方向：

- 分层、命名、与 mugsun-boot 的鉴权 / API 基址约定，以届时代码与文档站为准。
- 本仓预期**没有**服务端 Flyway；数据库迁移仍在 mugsun-boot。
- 平台差异用条件编译（`APP-ANDROID`、`APP-IOS`、`APP-HARMONY`、`MP-WEIXIN`、`WEB`），不要在运行时判断平台后再调别端的 API。
- uvue 在 App 端只支持 flex 布局和单个类选择器（不支持级联、伪类、grid）。UTS 写显式类型，不用 `any`；可空值用 `T | null`，先判空再用。
- `ms-` 前缀留给 uniappx-ui（easycom 自动引入），业务组件用 `app-` 前缀。
- 图标优先用 `ms-icon`。自带字体时：Android / 鸿蒙用 ttf，iOS / Web 用 woff2，小程序用子集化后的 base64（超过 64KB 拒绝）。
- 验证清单：微信开发者工具、iOS 模拟器、Android 真机、鸿蒙、手机微信预览；没验的端在提交说明里写明。界面改动要覆盖空状态、英文、暗色、窄屏。
- CI 不编译 uts / uvue，只跑 lint、node 测试、依赖扫描和 gitleaks；配置照 uniappx-ui：顶层只读权限、Action 钉 SHA、`npm ci --ignore-scripts`、check-workflows、osv、gitleaks 全历史扫描加每日定时。

## 本地构建

- 重型任务（构建、类型检查、e2e、测试容器、dev server 等）一律经仓库父目录 `mugsun/` 下的 `_locks/heavy.sh` 排队，同一时间只跑一个：
  ```bash
  ../_locks/heavy.sh <名字> <做什么> -- <命令...>
  # 例：../_locks/heavy.sh 平台后端工程师 "core 单测" -- mvn -q test
  ```
- 前面有任务时脚本每 30 秒重试并打印当前占用者；空闲内存低于 30% 时不开跑（退出码 75），先停掉闲置的 dev server 再试。
- 脚本会在未设置时自动设 `NODE_OPTIONS=--max-old-space-size=3072`，不要在命令里再调大。
- dev server 用完即停，不要挂着。

## 提交与推送

- Conventional Commits，主题中文；若存在 `.github/COMMIT_CONVENTION.md` 则按其执行，否则按组织默认指南。
- `CHANGELOG.md` 写在 `## [Unreleased]` 下，**每行不超过 80 字**。
- 不用 `git stash`。每项工作用独立的 `git worktree`，基于 `origin/main` 建分支；本地 `main` 若已分叉，不 reset、也不推。
- 不 force push，不 rebase 已推送的分支。
- 不开 PR，流程是：实测 → 代码质量官审查（涉及安全的再加安全官）→ 快进合入 `origin/main`。
- **只有分支最新的 commit 已经在分支上跑过同名必需检查、并且全绿，才可以快进推入 main。推送前用 `gh run list --commit <sha>` 自己确认。**（当前若尚无 CI，则以组织流程为准，有 workflow 后再按同名检查执行。）
- 删除或下线功能，先问用户。
- 本仓禁用 CloudAgent。
