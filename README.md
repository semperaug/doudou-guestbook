# 豆豆龙博客 · GitHub Pages 说明

## 地址

- 站点：https://semperaug.github.io/doudou-guestbook/
- 仓库：https://github.com/semperaug/doudou-guestbook
- 数据快照：https://semperaug.github.io/doudou-guestbook/data.json

## 发布模型

**分支直发（Deploy from a branch）**，不是 GitHub Actions。

Pages 设置：Source = `Deploy from a branch` → branch `main` → folder `/ (root)`。

选择理由：站点是预构建好的纯 HTML，不需要构建步骤。
**没有构建步骤就没有构建会失败** —— 这是给小型站点的正确取舍
（参见 `static-site-deploy` skill：不要加你不需要的构建步骤）。

## 目录里那些文件各自干什么

| 文件 | 作用 |
|---|---|
| `index.html` | 首页。由 `~/showcase/index.html` 同步而来 |
| `weekly-YYYY-MM-DD.html` | 每周周报页，89 份历史 |
| `data.json` | 实时数据快照，首页 JS 用 `?_=` 取 |
| `.nojekyll` | **告诉 Pages 跳过 Jekyll 构建**。缺了它，将来任何 `_` 开头的文件/目录会被静默丢弃 |
| `404.html` | 自定义 404 页 |
| `.gitignore` | 排除 `*.bak` 本地备份 |

## 本地工作目录

```
~/.hermes/profiles/writer/scripts/blog-sync-work/   ← git 工作区 = 网站
~/.hermes/profiles/writer/scripts/blog_sync.py       ← 同步脚本
~/showcase/index.html                               ← 首页的真正源头
```

**首页改的是 `~/showcase/index.html`，不是工作目录里的副本** ——
脚本每次运行都会用 showcase 版本覆盖工作目录的副本。

## 数据从哪来

`blog_sync.py` 读 `~/openclaw-kb/`：

| data.json key | 来源 |
|---|---|
| `weekly[]` | `编译产物/ai-trends/weekly-*.md` |
| `daily[]` | `原始素材/daily-brief/*.md` |
| `trending[]` | `原始素材/daily-news-push/digest_*.md` |
| `macro{}` | `原始素材/daily-brief/*.md` 的【宏观数据】段 |
| `cron{}` | `hermes cron list` |
| `github[]` | GitHub commits API |

## 缓存行为

GitHub Pages 对所有文件返回 `cache-control: max-age=600`（10 分钟）。
首页 JS 用 `?_=${Date.now()}` 绕过 CDN 缓存，所以刷新即最新。
weekly 页是静态 HTML，靠 10 分钟 TTL 自然过期 —— 接受。

## 已知边界

- **不跑 Lighthouse CI** —— 性能靠人工抽查
- **SEO 弱于纯静态** —— 早报/榜单是客户端 fetch 出来的，爬虫要执行 JS 才看得到
- **无自定义域名** —— 现在用 `semperaug.github.io`，路径前缀是 `/doudou-guestbook/`
