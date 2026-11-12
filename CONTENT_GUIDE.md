# 内容修改指南（速查版）

> **一句话：日常只改 `content/` 和 `public/`，改完 `git push` 就自动上线**，不用碰 `src/` 里的代码。
>
> 站点：https://ashyf0x.github.io/ ｜ 仓库：`A:/Github/Ashyf0x.github.io` ｜ 推送到 `master` 后 GitHub Actions 自动构建部署，约 1–3 分钟生效。

## 1. 想改什么，改哪个文件

| 想改的东西 | 文件 |
| --- | --- |
| 站点标题、姓名、头衔、学校、地点、邮箱、GitHub / Google Scholar 链接、导航菜单 | `content/config.toml` |
| 首页侧栏 "Research Interests" 标签 | `content/about.toml` 里的 `research_interests` |
| 自我介绍正文（About 段落） | `content/bio.md` |
| CV 页面全部内容 | `content/cv.md` |
| 论文信息（标题 / 作者 / 年份 / 会议 / 链接 / 摘要） | `content/publications.bib` |
| 论文缩略图 | `public/papers/*.png` |
| 头像 | `public/profile.png` |
| 首页板块标题、顺序、首页显示几篇论文 | `content/about.toml` 里的 `[[sections]]` |
| News 新闻 | `content/news.toml`（还需在 `about.toml` 里启用，见下文） |
| `/publications` 页标题与描述 | `content/publications.toml` |
| 网站图标 | `public/favicon.svg` |

## 2. 常见改法

### 改名字 / 简介 / 研究兴趣

- `content/config.toml`：`[site].title` 是站点标题，`[author].name` 是论文作者里高亮的名字，`institution`、`title`、`[social]` 下的邮箱和链接都在这里改。
- `content/bio.md`：普通 Markdown，`**加粗**`、`[文字](链接)`、`- 列表` 都能用。
- `content/about.toml`：侧栏兴趣标签是一个数组：

```toml
[profile]
research_interests = [
  "Time Series",
  "AI Agents"
]
```

注意：**"研究兴趣"在三个地方出现** —— 侧栏标签（`about.toml`）、简介正文（`bio.md`）、CV（`cv.md`），要一起改才一致。

### 加一篇论文

在 `content/publications.bib` 末尾追加一段（复制下面模板改内容）：

```bibtex
@inproceedings{yourpaper2026,
  selected = {true},
  title = {Your Paper Title},
  author = {First Author and Huihu Ding and Third Author},
  year = {2026},
  month = jan,
  booktitle = {AAAI 2026 (Oral)},
  description = {一句话说明这篇论文做了什么。},
  url = {https://arxiv.org/abs/xxxxx},
  doi = {10.xxxx/xxxxx},
  code = {https://github.com/xxx/xxx},
  preview = {yourpaper.png}
}
```

几个关键点：

- 第一行 `yourpaper2026` 是唯一标识，不能和已有条目重复。
- `selected = {true}` 才会出现在**首页**；不写或写 `{false}`，则只在 `/publications` 页出现。
- `preview` 只写文件名，图片放到 `public/papers/` 下；建议 4:3 白底 PNG、约 1200×900 的方法图。不需要缩略图就删掉这一行。
- `url` / `doi` / `code` / `description` / `preview` 都是可选字段，用不到直接删掉对应行；`title` / `author` / `year` / `booktitle` 建议保留。
- 作者之间用 `and` 分隔；写成 `Huihu Ding` 会自动高亮（与 `config.toml` 的 `[author].name` 对应）。
- `month` 用小写三字母（`jan` / `mar` / `jun` …），`booktitle` 里可以直接写 `(Oral)`、`(Poster)`。

### 改首页板块标题 / 显示数量

`content/about.toml` 里这一段控制首页的论文板块：

```toml
[[sections]]
id = "featured_publications"
type = "publications"
title = "Publications"
filter = "selected"
limit = 5
```

`title` 改标题，`limit` 改首页最多显示几篇（全部论文始终在 `/publications`）。

### 恢复 News（目前是隐藏的）

把 `content/about.toml` 末尾注释的 `#` 去掉，变成：

```toml
[[sections]]
id = "news"
type = "list"
title = "News"
source = "news.toml"
```

然后在 `content/news.toml` 里按这个格式加条目：

```toml
[[news]]
date = "2026-03"
content = "Our paper was accepted at AAAI 2026."
```

### 换头像 / 换缩略图

直接**覆盖同名文件**最省事：`public/profile.png`、`public/papers/xxx.png`。如果换了文件名，记得同步改 `config.toml` 的 `avatar` 或 `.bib` 里的 `preview`。文件名建议只用英文、数字、短横线，不要空格和中文；图片控制在几百 KB 以内。

### 加一个新页面（进阶）

新建 `content/xxx.md` 写正文，再新建 `content/xxx.toml`：

```toml
type = "text"
title = "Teaching"
source = "xxx.md"
```

最后在 `content/config.toml` 末尾加一段导航：

```toml
[[navigation]]
title = "Teaching"
type = "page"
target = "xxx"
href = "/xxx"
```

## 3. 本地预览（可选）

需要 Node.js 22+，第一次先 `npm install`。在项目目录执行：

```bash
npm run dev
```

浏览器打开 http://localhost:3000 ，保存文件会自动刷新，按 `Ctrl+C` 停止。
想检查正式构建则执行 `npm run build`，产物在 `out/`（**不用手动上传**，CI 会自己构建）。

## 4. 发布上线

```bash
git add -A
git commit -m "更新个人简介"
git push origin master
```

到 https://github.com/Ashyf0x/Ashyf0x.github.io/actions 看进度，变绿后按 `Ctrl+F5` 强刷页面即可看到新内容。

## 5. 注意事项 / 排错

- **不要改** `src/`、`node_modules/`、`out/`、`package-lock.json`；`out/` 是构建产物，每次构建都会被覆盖。
- 多语言（`i18n`）已关闭，内容只有 `content/` 这一份，不用管语言目录。
- TOML：字符串用英文双引号，数组元素用逗号分隔；BibTeX：值用 `{}` 包住，字段之间不需要逗号。
- 只在本地改文件**不会**上线，必须 `git push`。
- 改错了可以回滚：`git log --oneline` 找到上一次的提交编号，执行 `git revert <编号>` 再 push；或者直接把内容改回来重新提交。
- 如果推送报代理错误，检查 `git config --global --get http.proxy`，正常应为空（旧的 `127.0.0.1:7897` 配置已清理）。
