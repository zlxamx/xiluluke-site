# xiluluke.com Mac 与 AI 交接说明

版本：1.0

交接日期：2026-07-24

功能基线：`9fd0889`（Add automatic article cover selection）

## 1. 当前项目已经完成什么

网站已经正式上线，当前系统由四部分组成：

1. Jant：文章写作、发布、合集、精选、线程回复和媒体管理。
2. GitHub：Jant 内容镜像、定制模板、样式、站点级配置和版本历史。
3. Hugo：将 Markdown、模板、数据和静态资源构建为静态网站。
4. Cloudflare Pages：自动构建、托管、HTTPS、正式域名和部署回滚。

当前地址：

- 正式网站：https://xiluluke.com/
- WWW：https://www.xiluluke.com/（301 跳转到主域名）
- Jant 写作后台：https://luxi.blog/
- Jant 默认子域名：https://luxiblog.jant.blog/
- Cloudflare Pages 预览：https://xiluluke-site.pages.dev/
- GitHub：https://github.com/zlxamx/xiluluke-site

Jant 的 `NOINDEX` 应保持开启，避免 `luxi.blog` 与正式网站形成重复索引。

## 2. Mac 首次接手

### 2.1 安装基础工具

打开 Terminal：

```bash
xcode-select --install
```

安装 Homebrew 后：

```bash
brew install git gh hugo
```

检查：

```bash
git --version
gh --version
hugo version
```

Hugo 应使用 Extended 版本。Cloudflare 当前固定使用 `0.164.0`，本地优先使用相同版本或经过构建验证的兼容版本。

### 2.2 登录 GitHub

```bash
gh auth login
```

建议选择：

- GitHub.com
- HTTPS
- Login with a web browser

配置提交身份：

```bash
git config --global user.name "zlxamx"
git config --global user.email "你的 GitHub 邮箱"
```

### 2.3 克隆仓库

```bash
mkdir -p ~/Projects
cd ~/Projects
git clone https://github.com/zlxamx/xiluluke-site.git
cd xiluluke-site
```

Mac 本地项目目录：

```text
~/Projects/xiluluke-site
```

### 2.4 第一次构建

```bash
hugo server
```

浏览器访问：

```text
http://localhost:1313/
```

再运行正式构建检查：

```bash
hugo --minify
```

输出目录为 `public/`。不要把 `public/` 提交到 GitHub。

## 3. 每次让 AI 工作前

进入项目并同步：

```bash
cd ~/Projects/xiluluke-site
git switch main
git pull --ff-only
git status
```

把下面这段作为 AI 的固定开场指令：

```text
这是 xiluluke.com 的 Hugo 源码仓库。

开始工作前，请完整阅读：
1. .jant-sync
2. docs/MAC-HANDOFF.md
3. SITE-OPERATIONS.md
4. HOMEPAGE-OPERATIONS.md
5. COVER-OPERATIONS.md

Jant 是文章内容源。不要把长期定制写入 Jant 管理路径：
- content/**
- themes/jant/**
- data/jant.toml
- hugo.toml
- .gitignore
- README.md
- .jant-sync

长期定制应放在：
- layouts/**
- static/**
- data/luxi.toml

修改前先说明将影响哪些页面。修改后运行 hugo --minify，
检查 git diff，并向我汇报结果。未经确认不要直接推送 main，
不要修改 Cloudflare、DNS、域名或 Jant 同步设置。
```

## 4. 两种维护工作必须分开

### 4.1 内容维护：在 Jant 完成

以下操作统一在 `luxi.blog`：

- 新建、编辑或删除文章
- 发布文章和调整公开状态
- 设置 Featured
- 创建和管理合集
- 将文章加入合集
- 添加线程回复
- 上传文章图片和附件

Jant 是内容 source of truth。GitHub 中的 `content/` 是镜像，不是日常写作入口。

Jant 发布后：

1. Jant GitHub Sync 自动推送到 GitHub `main`。
2. GitHub Actions 运行 Hugo build check。
3. Cloudflare Pages 自动构建并发布。
4. 正式网站自动更新。

### 4.2 网站定制：在 GitHub 仓库完成

安全修改入口：

| 任务 | 文件或目录 |
| --- | --- |
| 首页头条 | `data/luxi.toml` 的 `headline_slug` |
| 首页固定文章 | `data/luxi.toml` 的 `fixed_articles` |
| 首页合集 | `data/luxi.toml` 的 `featured_collections` |
| 手工文章封面 | `data/luxi.toml` 的 `[covers]` |
| 联系方式 | `data/luxi.toml` 的 `[contact]` |
| 首页项目 | `data/luxi.toml` 的 `[[projects]]` |
| 服务 | `data/luxi.toml` 的 `[[services]]` |
| 页面模板 | 根目录 `layouts/` |
| 视觉样式 | `static/editorial.css` |
| 固定图片 | `static/images/` |

## 5. Jant 会覆盖的路径

以 `.jant-sync` 的 `managed_globs` 为最终依据。

当前 Jant 管理：

```text
content/**
themes/jant/**
data/jant.toml
hugo.toml
.gitignore
README.md
.jant-sync
```

不要直接修改 `themes/jant/**` 做长期定制。下一次 Jant push 可能覆盖修改。

不要在 `content/**` 中放手写产品页、服务页或说明页。Jant 可能在后续同步中删除不再生成的文件。

## 6. 安全的 AI 开发与发布流程

建议每个任务使用独立分支：

```bash
git switch main
git pull --ff-only
git switch -c codex/任务名称
```

AI 修改后：

```bash
hugo --minify
git status
git diff
```

确认后提交：

```bash
git add .
git commit -m "简明的修改说明"
git push -u origin codex/任务名称
```

Cloudflare 可为非生产分支生成预览。确认页面和手机端没有问题后，再把该分支合并到 `main`。

`main` 更新后，Cloudflare Pages 自动发布正式网站。

小型且风险很低的修改也可以直接在 `main` 完成，但仍然必须先 `git pull --ff-only`、本地构建并检查 `git diff`。

## 7. 当前 Cloudflare 配置

除非明确进行基础设施迁移，否则不要修改：

```text
Pages project: xiluluke-site
Production branch: main
Build command: hugo --minify -b https://xiluluke.com
Build output directory: public
HUGO_VERSION: 0.164.0
```

域名：

```text
xiluluke.com              正式主域名
www.xiluluke.com          301 跳转到主域名
xiluluke-site.pages.dev   Pages 备用和排障地址
luxi.blog                 Jant 写作后台，保持 NOINDEX
```

不要为了排查普通页面问题而删除 Pages 项目、DNS 记录、自定义域名或重定向规则。

## 8. 页面同步关系

- 公开文章自动生成 `/{slug}/`。
- 所有公开文章自动进入 `/archive/`。
- 首页最近发布最多 5 篇；固定文章优先，其余位置用最新公开文章补齐。
- Featured 文章进入 `/featured/`，也会参与首页头条的自动回退。
- 文章加入 Jant 合集后，自动进入 `/{合集slug}/`。
- Jant 合集目录自动进入 `/collections/`。
- 首页合集入口由 `featured_collections` 手动选择，标题和文章数来自 Jant。
- 加入 `products` 合集的文章自动进入 `/products/`。
- 首页“个人项目”卡片不会因加入 `products` 自动新增，仍需编辑 `[[projects]]`。
- `/seedraft/` 是定制产品中心，介绍内容由模板维护，相关文章按约定合集归类。

详细规则见：

- `docs/xiluluke-site-maintenance-manual.pdf`
- `HOMEPAGE-OPERATIONS.md`
- `COVER-OPERATIONS.md`

## 9. 出现问题时

按以下顺序排查：

1. Jant：GitHub Sync 是否显示 `Last synced`。
2. GitHub：是否出现预期的 `[jant-sync]` 提交。
3. GitHub Actions：`Hugo build check` 是否通过。
4. Cloudflare Pages：最新 Production deployment 是否成功。
5. 打开 `xiluluke-site.pages.dev`。
6. 再打开 `xiluluke.com`。

如果 Pages 预览正常、正式域名异常，问题通常在域名、缓存或重定向层。

如果两边都异常，优先查看最近的构建日志和代码提交。

## 10. 回滚

- 内容错误：在 Jant 修正并重新发布。
- 代码错误：revert 对应人工提交，再让 `main` 重新部署。
- 紧急页面恢复：在 Cloudflare Pages 的 Deployments 中回滚到上一条正常的生产部署。
- 不要使用 `git reset --hard`、强制推送或删除仓库作为常规恢复方法。

## 11. 凭据与安全

仓库中不会保存：

- GitHub 登录凭据
- Jant 登录凭据
- Cloudflare 登录凭据
- API token
- SSH 私钥

Mac 需要分别登录 GitHub、Jant 和 Cloudflare。不要把 token 或密码交给 AI，也不要提交到仓库。

AI 可以使用已经登录的本地工具执行 Git 操作，但任何 DNS、域名、生产回滚和账户设置变更都应先取得明确确认。

## 12. Mac 接手完成的判断

满足以下条件即完成交接：

- [ ] Mac 已安装 Git、GitHub CLI 和 Hugo Extended。
- [ ] `gh auth status` 显示已登录。
- [ ] 仓库已克隆到 `~/Projects/xiluluke-site`。
- [ ] `git status` 显示与 `origin/main` 同步。
- [ ] `hugo server` 能在本地打开首页和文章。
- [ ] `hugo --minify` 无错误。
- [ ] AI 已阅读 `.jant-sync` 与本交接文档。
- [ ] 创建测试分支并成功推送。
- [ ] Cloudflare 分支预览正常。
- [ ] 未修改或覆盖 Jant 管理路径。

完成后，Windows 本地副本不再是必需品。GitHub、Jant 和 Cloudflare 才是持续运行与恢复网站所需的线上系统。
