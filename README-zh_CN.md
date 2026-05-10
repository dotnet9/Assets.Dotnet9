# Assets.Dotnet9

CodeWF 站点资源与内容仓库。

English version: [README.md](./README.md)

这个仓库不只是图片仓库，它实际上承担了网站“文件型内容库”的角色，保存并组织如下内容：

- 博客文章
- 文档页面
- 工具元数据
- 时间线数据
- 友情链接
- 站点级 markdown 页面
- 文章配图和站点资源

## 关联仓库

- 网站源码仓库：[CodeWF](https://github.com/dotnet9/CodeWF)
- 资源仓库：[Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9)

## 目录说明

```text
2019/ ... 2026/      按年份、月份组织的文章正文、sidecar 元数据与配图
site/
  about.md           关于页内容
  Privacy.md         隐私页内容
  albums.json        专题元数据
  categories.json    分类元数据
  friend-links.json  友情链接元数据
  timelines.json     时间线数据
  doc/               文档导航与正文
  tools/             工具元数据与预览图
  pays/              打赏页资源
  banners/           页面横幅资源
  favicon/           站点图标资源
```

## 在线角色

`CodeWF` 网站通过 `Site:LocalAssetsDir` 直接读取本仓库内容，因此这个仓库的维护标准应该接近一个轻量 CMS：

- 路径稳定
- 元数据完整
- markdown 可审阅
- 资源命名可复用
- 结构规则可传递

## 内容规范

- 中文版：[CONTENT_GUIDE-zh_CN.md](./CONTENT_GUIDE-zh_CN.md)
- English: [CONTENT_GUIDE.md](./CONTENT_GUIDE.md)

## 维护建议

- 文章正文统一放在 `YYYY/MM/slug.md`
- 文章元数据统一放在同名 `YYYY/MM/slug.yml`
- 封面、配图尽量使用有语义的文件名
- 新增分类、专题、文档、工具时同步维护对应的 `site/*.json` 或 `site/**/navigation.json`
- 不提交本地临时文件、导出缓存或无关桌面文件
