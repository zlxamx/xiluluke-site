# 首页文章陈列维护

首页的文章陈列由 `data/luxi.toml` 中的 `[home]` 配置控制。这个文件不属于 Jant 的托管路径，不会在 Jant 同步文章时被覆盖。

## 指定头条

找到文章网址最后一段。例如：

```text
https://xiluluke.com/jj19/
```

对应的 slug 是 `jj19`。将它写入：

```toml
[home]
headline_slug = "jj19"
```

如果留空、文章不存在或文章不是公开状态，首页会依次回退到：

1. 最新一篇 Featured 文章；
2. 最新一篇公开文章。

## 固定推荐文章

`fixed_articles` 控制首页“最近发布”列表中的固定文章：

```toml
fixed_articles = ["ai03", "jk02", "xy4"]
```

文章会按照数组中的顺序优先展示，空余位置自动用最新公开文章补满，总数最多五篇。无效、重复或非公开的 slug 会被自动忽略。

若希望该区域完全按照发布时间自动更新，保持：

```toml
fixed_articles = []
```

## 发布

修改并推送到 GitHub 的 `main` 分支后，Cloudflare Pages 会自动构建并发布，无需在 Cloudflare 中手动操作。
