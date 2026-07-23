# xiluluke.com 站点维护说明

## 站点结构

- Jant 继续负责文章写作、发布和 GitHub Sync。
- Hugo 负责把同步到仓库的内容构建成新的访客端网站。
- 首页、目录、正文、产品中心和联系区使用仓库中的定制模板。
- `data/luxi.toml` 保存个人项目、服务和联系方式等站点级信息。

## 主要页面

- `/`：个人首页
- `/archive/`：全部文章
- `/featured/`：精选文章
- `/collections/`：专栏集合
- `/products/`：全部产品
- `/seedraft/`：文芽 Seedraft 产品内容中心
- `/services/`：对外服务
- `/about/`：关于

## 日常更新

文章仍然在 Jant 中编辑和发布。同步完成后，GitHub 会运行一次 Hugo 构建检查。

需要修改项目、服务、联系方式或首页固定文案时，优先编辑：

```text
data/luxi.toml
```

视觉样式集中在：

```text
static/editorial.css
```

## 本地预览

安装 Hugo Extended 0.164.0 或更高版本，然后在仓库根目录运行：

```bash
hugo server
```

浏览器访问 `http://127.0.0.1:1313/`。

正式构建：

```bash
hugo --minify
```

输出目录为 `public/`。

## 发布前检查

1. 首页、文章目录、任意一篇正文、产品页和服务页均能打开。
2. 微信按钮显示 `zlxamx`，邮箱按钮调用 `Seedraft@outlook.com`。
3. Seedraft 官网和两个产品截图仍然有效。
4. GitHub Actions 的 `Hugo build check` 通过。
5. 域名切换前保留 Jant 管理端的稳定访问地址。

## 内容同步边界

Jant 管理 `content/` 中的文章数据。不要把长期维护的手写页面放进 `content/`；定制页面应放在 `layouts/`、`data/` 或 `static/`，避免后续同步覆盖。
