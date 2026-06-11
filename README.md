# 一键清理缓存

> Chrome 浏览器扩展 — 一键清理浏览数据，永久保留密码与自动填表信息。


<p align="center">
  <a href="https://chrome.google.com/webstore/category/extensions">
    <img src="https://img.shields.io/badge/Chrome-Extension-blue?logo=googlechrome&logoColor=white" alt="Chrome Extension">
  </a>
  <img src="https://img.shields.io/badge/manifest-v3-orange?logo=googlechrome" alt="Manifest V3">
  <img src="https://img.shields.io/github/license/yourname/chrome-cleaner-extension?color=green" alt="License">
  <img src="https://img.shields.io/badge/version-1.1.0-purple" alt="Version">
</p>

---

## ✨ 功能特性

| 功能 | 说明 |
|------|------|
| 🗑️ 一键清理 | 点击工具栏图标，立即清理，无需弹窗 |
| 🕒 全量清理 | 清理全部时间的浏览数据 |
| 🔒 密码保护 | **已保存的密码永远不会被清理** |
| 📝 填表保护 | **自动填表信息永远不会被清理** |
| 📥 下载保护 | 下载记录不会被清理 |

### 清理范围

- ✅ 浏览历史记录
- ✅ Cookie（网站登录状态会失效）
- ✅ 缓存的图片和文件
- ✅ 本地存储（localStorage）
- ❌ 已保存的密码（永久保留）
- ❌ 自动填表信息（永久保留）
- ❌ 下载记录（永久保留）

---

## 📥 安装方式

### 方式一：从 Chrome 应用商店安装（待上架）

> 上架后在此处添加应用商店链接。

### 方式二：开发者模式加载（推荐）

1. 下载本仓库到本地：
   ```bash
   git clone https://github.com/yclxw/qlhc
   ```

2. 打开 Chrome，在地址栏输入：
   ```
   chrome://extensions/
   ```

3. 右上角开启「**开发者模式**」

4. 点击「**加载已解压的扩展程序**」

5. 选择本仓库的 `预算一体化清理缓存/` 目录

6. 扩展安装完成，工具栏会出现图标 ✅

---

## 🚀 使用方法

安装完成后，**无需任何设置**：

1. 浏览网页一段时间后，点击工具栏中的 **「清理预算一体化缓存」** 图标
2. 图标短暂显示 `...`（清理中）
3. 清理完成后图标显示 `✓`（绿色，2.5 秒后消失）
4. 若清理失败，图标显示 `✗`（红色）

> ⚠️ **注意**：Cookie 被清理后，网站登录状态会失效，需要重新登录。

---

## 📂 项目结构

```
一键清理缓存/
├── manifest.json          # 扩展配置（Manifest V3）
├── background.js         # Service Worker（核心清理逻辑）
├── icons/                 # 扩展图标（16/32/48/128 px）
│   ├── icon16.png
│   ├── icon32.png
│   ├── icon48.png
│   └── icon128.png
├── convert-logo.html    # 图标转换工具（logo.jpg → PNG）
├── convert_logo.py      # 图标转换脚本（需 Pillow）
└── README.md            # 本文件
```

---

## 🔧 技术实现

### 核心 API

使用 Chrome `browsingData` API 进行数据清理：

```javascript
// background.js 核心逻辑
chrome.browsingData.remove(
  { since: 0 },              // 0 = 全部时间
  {
    cache:        true,       // 清理缓存
    cookies:     true,       // 清理 Cookie
    history:      true,       // 清理历史记录
    localStorage: true,       // 清理本地存储
    // ❌ passwords: 不传 = 不清理
    // ❌ formData: 不传 = 不清理
    // ❌ downloads: 不传 = 不清理
  }
);
```

### 为什么密码一定安全？

`chrome.browsingData.remove()` 采用**白名单机制**——只清理你显式传入的类型。代码中**从未传入** `passwords` 和 `formData`，从 API 层面确保它们永远不会被触碰。

### Manifest V3

本扩展使用最新的 **Manifest V3** 规范：
- Service Worker 替代 Background Page
- 更严格的权限控制
- 更好的性能和安全性

---

## 🔒 隐私说明

- **本扩展不收集任何用户数据**
- **本扩展不联网，所有操作均在本地完成**
- **本扩展不读取、不上传任何浏览内容**
- 清理范围完全由 `browsingData` API 白名单控制，无法越界

---

## 🐛 问题排查

### 扩展无法加载

**错误信息**：`Could not load icon 'icons/icon16.png'`

**解决方法**：图标文件不存在，需要先从 `logo.jpg` 生成图标：

1. 确保 `logo.jpg` 在扩展根目录
2. 用 Chrome 打开 `convert-logo.html`
3. 点击「生成并下载全部图标」
4. 将下载的 4 个 PNG 放入 `icons/` 目录
5. 回到 `chrome://extensions/` 点击「重新加载」

### 清理后网站需要重新登录

这是正常现象——清理 Cookie 会导致网站登录状态失效。

---

## 📝 更新日志

### v1.1.0（当前版本）

- ✨ 去掉弹窗，点击图标直接清理
- ✨ 名称更新为「清理预算一体化缓存」
- ✨ 徽章提示（清理中 `...` / 成功 `✓` / 失败 `✗`）

### v1.0.0

- 🎉 首次发布
- ✅ 支持选择清理项目
- ✅ 支持时间范围选择
- ✅ 密码与自动填表信息永久保护

---

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建特性分支：`git checkout -b feature/your-feature`
3. 提交更改：`git commit -m 'Add some feature'`
4. 推送分支：`git push origin feature/your-feature`
5. 提交 Pull Request

---

## 📄 License

MIT License — 详见 [LICENSE](LICENSE) 文件。

---

## 🙏 致谢

- [Chrome Extensions Documentation](https://developer.chrome.com/docs/extensions/)
- [browsingData API](https://developer.chrome.com/docs/extensions/reference/api/browsingData)

---

<p align="center">
  Made with ❤️ by <a href="https://github.com/yourname">yourname</a>
</p>
