# mugsun-app

**中文** | [English](README_EN.md)

![uni-app x](https://img.shields.io/badge/uni--app%20x-UTS-blue)
![Vue](https://img.shields.io/badge/Vue-3-4FC08D?logo=vuedotjs&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

**Mugsun 低代码平台的移动端**——基于 uni-app x（UTS），覆盖 Android / iOS / 鸿蒙 / 微信小程序，对接 [mugsun-boot](https://github.com/mugsun/mugsun-boot) 后端。

## ✨ 功能

- 登录鉴权（对接 Sa-Token）
- 工作台 / 待办 / 消息 / 个人中心等移动办公页面
- 与 mugsun-pc 共用同一套后端菜单与权限体系

## 🗂 仓库全景

与平台其余仓库**平级 clone**：

```
mugsun/
├── mugsun-core      # 后端 BOM + Starter
├── mugsun-boot      # 后端服务（:8080）
├── mugsun-pc        # PC 管理端
├── mugsun-track     # 埋点 SDK
└── mugsun-app       # 本仓（移动端）
```

## 🚀 快速开始

1. 使用 [HBuilderX](https://www.dcloud.io/hbuilderx.html) 或 uni-app x CLI 打开本仓
2. 启动本地 [mugsun-boot](https://github.com/mugsun/mugsun-boot)
3. 在 `api/` 或配置中指向后端地址，运行到模拟器或真机

## ⭐ Star History

[![Star History Chart](https://api.star-history.com/svg?repos=mugsun/mugsun-app&type=Date)](https://star-history.com/#mugsun/mugsun-app&Date)

## 📄 许可证

[MIT License](LICENSE)

---

如果这个项目对你有帮助，欢迎 Star ⭐ 支持一下！
