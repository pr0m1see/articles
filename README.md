# 飞函文章预览

站点地址：https://pr0m1see.github.io/articles/

本仓库保存公开的文章预览。GitHub Pages 使用 GitHub Actions 部署，每次推送到 `main` 后发布 `site/` 目录，也可以在 Actions 页面手动运行 **Deploy Pages**。

界面参考 Claude Articles 的暖白配色、衬线标题、侧栏比例与文章卡片，保留飞函品牌，省略产品导航、订阅和复杂筛选。首页尚无文章时显示待发布状态。英文字体使用自托管的 Source Serif 4，许可见 `site/assets/fonts/LICENSE.md`。

## 目录

```text
site/
  index.html               # 文章入口
  status/                  # 发布状态页及每日统计快照，由 Content Bot 自动维护
  404.html                 # 页面不存在时的提示
  assets/styles.css        # 首页和文章共享的阅读样式
  a/<revision_id>/
    index.html             # 后续由 Content Bot 导出的完整文章页面
    assets/*.webp          # 该版本文章的预览配图
templates/
  article.html             # 文章导出模板，不直接部署
  index.html               # 自动生成首页，保留现有版式
  card.html                # 首页文章卡片模板
.github/workflows/
  pages.yml                # 静态文件部署流程
```

## 文章模板

`templates/article.html` 为导出时使用的文本模板，不在浏览器中执行替换。导出器需要替换以下占位符：

- `{{title}}`：文章标题，按 HTML 文本转义。
- `{{description}}`：文章摘要，按 HTML 属性转义。
- `{{body_html}}`：Markdown 渲染后的正文 HTML，不重复包含页面主标题；图片路径相对文章目录，例如 `./assets/image-01.webp`。

页面保存为 `site/a/<revision_id>/index.html`，对应 `/articles/a/<revision_id>/`。同一文章版本使用固定链接。模板中的首页和样式使用相对路径，以支持 GitHub Pages 的 `/articles/` 子路径。

Content Bot 导出器还读取 `templates/index.html` 和 `templates/card.html`，按 `site/articles.json` 中的文章目录重新生成首页。新文章、WebP 配图、目录和首页在一次 Git 提交中更新；旧文章保留。三个模板必须一起部署后才能启用自动上传。目录中的时间为冻结时间，卡片按 UTC+8 显示日期。

预览图片计划使用保留原尺寸、质量参数 85 的 WebP；原始 Bundle 及原图继续保留在 Content Bot 中。文章预览持续保留，不做滚动清理。

## 当前接入状态

Content Bot 已接入 Markdown 导出、WebP 图片、文章自动上传和飞函预览按钮，文章首页随目录更新。发布状态页由同一 Content Bot 服务维护。

## 发布状态页

[查看发布状态](https://pr0m1see.github.io/articles/status/)。页面展示七个平台最近 90 个完整自然日的发布记录：首次成功为绿，处理后成功为黄，未确认成功为红，没有任务为灰。

Content Bot 在 Asia/Singapore 每天 00:00 重新统计并提交 `site/status/index.html` 和 `site/status/data.json`，沿用本仓库的 Pages 部署。统计只包含安全状态说明和公开文章链接；没有凭据、账号或任务身份。页面与数据由 Content Bot 状态页模块生成，样式和数据格式应在 Content Bot 维护，不手工改写生成文件。首页入口同时保存在 `templates/index.html`，后续文章上传不会移除它。

## 本地预览

在仓库根目录执行 `python3 -m http.server 8080 --directory site`，打开 `http://localhost:8080/`。线上 404 页的返回链接固定为 `/articles/`。

GitHub 仓库的 **Settings → Pages → Source** 选择 **GitHub Actions**。工作流使用 GitHub 自动提供的 `GITHUB_TOKEN` 部署，无需另配部署 Secret；Content Bot 后续自动上传所需的凭据另行配置。
