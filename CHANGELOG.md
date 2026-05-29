# Changelog

本文件记录「更好的B站分享按钮」的所有重要变更。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/)。

## [2026.5.29] - 2026-05-30

### Added
- 新增对 `t.bilibili.com`（动态页面）的 `@match` 匹配支持
- opus/动态页面标题获取增加多层回退策略：按 `.opus-module-title__text` → `.opus-module-title__content` → `.opus-module-title` → `.dyn-card-opus__summary` 顺序尝试，最终回退到 `document.title` 并清理 bilibili 后缀
- opus/动态页面 UP 主获取增加多层回退策略：按 `.opus-module-author__name` → `.bili-dyn-title__text` → `.bili-dyn-title` 顺序尝试
- 新版阅读页面（opus）在右侧侧边栏（`.side-toolbar__box`）末尾插入独立的分享按钮，含 SVG 分享图标 + "分享"文字标签
- 新版阅读页面分享按钮自动继承已有侧边栏按钮的颜色样式
- 新版阅读页面插入按钮时自动移除底部原始分享区域（`.opus-module-bottom__share`）
- 重试机制增加上限保护（最多 20 次 / 10 秒），避免无限轮询

### Changed
- 版本号从 `2026.2.9` 升级至 `2026.5.29`
- opus 页面标题/UP 主获取方式从 XPath 查询重构为 CSS class 选择器 + 多层回退，提升稳定性与可维护性
- `showSuccessMessage` 定位按钮增加多重回退：`#BetterShare` → `#share-btn-outer` → `.side-toolbar__action.share` → `.icon-ctnr .BetterShare`，并为 `fontSize` 增加 `'14px'` 默认值，防止空引用
- 提示消息 `zIndex` 从 `9999` 提升至 `99999`，确保在 opus 页面浮层之上
- `replaceVideoShareSpan` 从"移除旧按钮→创建新按钮"重构为事件拦截模式（`addEventListener` + 捕获阶段），保留原有 DOM 结构不变
- `replaceLiveShareSpan` 大幅精简：移除复杂的样式复制逻辑和 span 创建，仅替换 SVG 图标
- 直播页 SVG 图标 `fill` 从硬编码 `#979797` 改为 `currentColor`，自动适配主题色
- `init()` 函数中 opus 条件扩展，同时匹配 `t.bilibili.com` 主机名

## [2026.2.9] - 初始版本

### Features
- 替换 B 站原有分享按钮，点击后获取页面标题和 UP 主信息
- 调用 `https://api.bilibili.com/x/share/click` API 生成短链接
- 将格式化文本（`标题：xxx\nUP主：xxx\n链接：短链接`）复制到剪贴板
- 复制成功后显示浮动黑色提示消息（1 秒后自动消失）
- 支持页面类型：
  - 视频页面（`/video`）
  - 直播页面（`live.bilibili.com`）
  - 阅读文章页面（`/read`）
  - 新版阅读页面（`/opus`）
  - 稍后再看页面（`/list`）
- 稍后再看页面自动从 URL 参数提取 `bvid`，转换为标准视频链接
- 样式继承：通过 `getComputedStyles()` 获取原按钮样式保持视觉一致
- 重试机制：使用 `setTimeout` 每 500ms 重试直到找到目标 DOM 元素
- 模拟真实请求参数：随机生成 `build`、`buvid`、`platform`、`shareMode`
