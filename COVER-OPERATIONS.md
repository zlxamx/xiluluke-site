# 文章封面维护

网站按照以下顺序选择文章封面：

1. `data/luxi.toml` 中手工指定的封面；
2. Jant 文章 `media` 中的第一张图片附件；
3. 正文中出现的第一张图片；
4. 没有图片时使用文字占位。

首页头条会使用同一规则；若头条文章完全没有图片，则回退到站点默认头像。文章的社交分享图也优先使用同一封面。

## 手工指定封面

在 `data/luxi.toml` 的 `[covers]` 下添加文章 slug：

```toml
[covers]
jj19 = "/images/covers/jj19.jpg"
```

站内图片应放入 `static/images/covers/`，配置路径从 `/images/covers/` 开始。也可以直接填写完整的远程图片 URL。

删除对应配置后，网站会恢复自动选择。
