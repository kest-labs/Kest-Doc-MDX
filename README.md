# Kest-Doc-MDX

这是 Kest 文档的内容仓库，用来存放文档正文、目录元数据和图片等内容资源。

它的职责很明确：

- 只放内容
- 只维护内容结构和元数据
- 不负责页面渲染
- 不负责站点路由实现
- 不负责 UI、主题、导航组件和 SEO 逻辑

真正读取并渲染这个仓库内容的是另一个文档站项目。在当前工作区里，对应项目是 `kest-docs`。

## 仓库定位

这个仓库应被视为文档内容的单一事实来源。

适合放在这里的内容：

- MDX 文档正文
- `_meta.json` 导航元数据
- 文档使用到的图片资源
- OpenAPI 规范文件
- 内容 schema 或约束文件

不适合放在这里的内容：

- React 页面组件
- Next.js 路由代码
- 站点布局、主题、侧边栏渲染逻辑
- 搜索实现
- 埋点、鉴权、接口代理等站点能力

一句话理解：

这个仓库负责“写什么”，文档站仓库负责“怎么展示”。

## 目录结构

当前建议目录结构如下：

```text
content/
  zh-Hans/
    docs/
      _meta.json
      get-started/
        _meta.json
        product-introduction.mdx
        quickstart.mdx
        using-this-document-in-a-programming-tool.mdx
    developers/
    changelog/
    faq/
  en-US/
    docs/
    developers/
    changelog/
    faq/
  shared/
    images/
openapi/
schemas/
```

各目录用途：

- `content/<locale>/docs/`
  放产品文档内容
- `content/<locale>/developers/`
  放开发者文档、SDK、鉴权、Webhook 等内容
- `content/<locale>/changelog/`
  放更新日志内容
- `content/<locale>/faq/`
  放 FAQ 内容
- `content/shared/images/`
  放多语言共用图片
- `openapi/`
  放接口规范文件
- `schemas/`
  放内容相关 schema 或约束文件

## 多语言目录怎么放

当前建议按 locale 分目录，不同语言不要混写在一个文件里。

示例：

```text
content/zh-Hans/docs/get-started/product-introduction.mdx
content/en-US/docs/get-started/product-introduction.mdx
```

规则：

- `zh-Hans` 表示简体中文
- `en-US` 表示英文
- 相同页面在不同语言下应保持相同的相对路径和 slug

例如：

- 中文：`content/zh-Hans/docs/get-started/quickstart.mdx`
- 英文：`content/en-US/docs/get-started/quickstart.mdx`

推荐约定：

- 中文作为源语言
- 英文作为翻译版本
- 如果某个语言版本暂时缺失，不要临时改 slug；优先补齐对应翻译文件

在最小阶段，不建议做复杂的“自动回退到英文”策略，这类逻辑应由 docs-site 决定，而不是在内容仓库里约定特殊写法。

## 文档编写规范

### 文件命名

- 使用小写英文和短横线命名
- 不使用空格
- 不使用中文文件名
- 文件名应稳定，不要频繁重命名

推荐：

- `product-introduction.mdx`
- `quickstart.mdx`
- `using-this-document-in-a-programming-tool.mdx`

不推荐：

- `QuickStart.mdx`
- `产品介绍.mdx`
- `getting_started.mdx`

### slug 规则

页面 URL 一般由目录和文件名共同决定。

例如：

- 文件：`content/zh-Hans/docs/get-started/product-introduction.mdx`
- URL：`/zh-Hans/docs/get-started/product-introduction`

因此：

- 不要随意改目录名
- 不要随意改文件名
- slug 一旦对外使用，修改前要确认 docs-site 是否需要做重定向

### frontmatter 要写哪些字段

当前最小阶段，建议每篇文档至少写这些字段：

```yaml
---
title: 产品介绍
description: 产品概览、核心价值与阅读起点说明。
status: stable
order: 10
summary: 先了解 Kest 是什么，以及这套文档覆盖哪些内容。
lastUpdated: 2026-03-31
---
```

字段说明：

- `title`
  页面标题，必填
- `description`
  页面描述，必填
- `status`
  页面状态，建议填写 `stable | beta | deprecated`
- `order`
  同级排序值，数字越小越靠前
- `summary`
  页面摘要，用于列表或卡片说明
- `lastUpdated`
  更新时间，建议使用 `YYYY-MM-DD`

预留字段：

- `icon`
- `tags`

这两个字段后续可以使用，但最小阶段不是强依赖。

### `_meta.json` 的作用

`_meta.json` 用来控制目录层级、分组标题和页面顺序。

例如：

```json
{
  "title": "开始使用",
  "clickable": false,
  "collapsible": false,
  "pages": [
    "product-introduction",
    "quickstart",
    "using-this-document-in-a-programming-tool"
  ]
}
```

含义：

- `title`
  分组名称
- `clickable`
  该分组是否可点击
- `collapsible`
  该分组是否可折叠
- `pages`
  子页面顺序

## 图片放哪里

图片统一放在以下目录之一：

- `content/shared/images/`
  多语言共用图片
- `content/<locale>/...` 下的局部资源目录
  仅当图片只服务某个特定语言页面时再考虑

最推荐的做法是先放到：

- `content/shared/images/`

命名建议：

- 全小写
- 短横线命名
- 文件名表达清楚用途

例如：

- `content/shared/images/auth-flow.png`
- `content/shared/images/dashboard-overview.png`

不要使用：

- `图片1.png`
- `final-final-v2.png`
- 带空格的文件名

## 图片怎么引用

当前 `kest-docs` 已经支持在渲染阶段自动识别 MDX 里的图片路径，并将仓库内图片路径转换为最终可访问地址。

内容作者不需要手动写 CDN URL。

推荐写法有两种：

1. 引用当前文档目录附近的相对图片

```mdx
![登录流程](./images/login-flow.png)
```

例如当前文档是：

```text
content/zh-Hans/docs/get-started/quickstart.mdx
```

则上面的写法会按当前文档目录解析为：

```text
content/zh-Hans/docs/get-started/images/login-flow.png
```

2. 引用共享图片目录

```mdx
![鉴权流程](/content/shared/images/auth-flow.png)
```

适用场景：

- 多语言共用图片
- 多篇文档共用图片
- 希望路径稳定，不依赖当前文档所在目录

还支持以下情况：

- 外部绝对 URL 图片

```mdx
![外部图片](https://cdn.example.com/demo.png)
```

- HTML `img`

```mdx
<img src="./images/login-flow.png" alt="登录流程" />
```

当前规则：

- 仓库内图片路径会由 docs-site 自动处理
- 已经是 `http://` 或 `https://` 的图片地址会原样保留
- `data:` 图片地址会原样保留

不建议：

- 直接在内容里硬编码业务 CDN 域名，除非这是明确要求
- 使用难以理解的相对路径层级
- 从文档路径跳出 `content/` 根目录

更稳妥的习惯是：

- 共享图片放 `content/shared/images/`
- 页面局部图片放在当前文档附近的 `images/` 目录

## 链接怎么写

普通 Markdown 链接可以直接使用：

```mdx
[快速开始](/zh-Hans/docs/get-started/quickstart)
```

```mdx
[OpenAI](https://openai.com)
```

当前 docs-site 行为：

- 站内链接按站内路由处理
- 外链会按外链方式渲染
- `mailto:`、`tel:`、`#锚点` 也可以正常使用

## 代码块怎么写

行内代码直接写：

```mdx
使用 `pnpm dev` 启动开发环境。
```

块级代码使用 fenced code block：

````mdx
```ts
import { Kest } from "@kest/sdk";

const client = new Kest({
  apiKey: process.env.KEST_API_KEY,
});
```
````

推荐总是带语言标记，例如：

- `ts`
- `tsx`
- `js`
- `bash`
- `json`
- `yaml`

例如：

````mdx
```bash
pnpm install
pnpm dev
```
````

当前 docs-site 已支持：

- fenced code block 渲染
- 常见语言的语法高亮
- 深浅色主题下的代码高亮
- 长代码块横向滚动

如果你不写语言标记，代码块仍然可以渲染，但会按普通文本高亮处理。

## 改完内容后会不会自动发布

这个仓库本身不负责发布。

是否自动发布，取决于 docs-site 的 CI/CD 配置，而不是这个内容仓库本身。

按当前本地代码状态看：

- 这个 MDX 仓库里还没有看到 GitHub Actions 或自动发布配置
- `kest-docs` 仓库里目前有 CI 检查，但没有看到明确的自动部署工作流

因此，当前不要默认认为：

- 改完内容就一定会自动发布
- 合并到 `main` 就一定会自动上线

更稳妥的理解是：

- 这个仓库负责提交内容
- docs-site 负责读取、构建和发布
- 是否自动发布要看 docs-site 后续是否接入部署流程

## 能不能直接改 `main`

默认不建议直接改 `main`。

推荐流程：

1. 新建功能分支
2. 提交内容修改
3. 发起 PR
4. 通过 review 后再合并到 `main`

原因：

- 文档改动也可能影响线上页面结构
- frontmatter、slug、图片路径改错后会直接影响 docs-site 构建
- 通过 PR 更容易做 review 和追踪变更

所以，本仓库默认协作规则应当是：

- 通过 PR 修改
- 不直接向主分支推送

如果后续仓库管理员明确允许少量 typo 直接修复，再单独补充规则；但默认不要这样做。

## 与 docs-site 的关系

这个仓库不是最终对外网站。

它和 docs-site 的关系是：

- 本仓库提供内容
- docs-site 拉取内容
- docs-site 解析 `_meta.json`
- docs-site 编译 `.mdx`
- docs-site 负责路由、布局、SEO、主题和最终发布

在当前工作区里，可以把关系理解成：

- `Kest-Doc-MDX`：内容仓库
- `kest-docs`：文档站仓库

后续如果 docs-site 接入完成，新增文档的基本流程会变成：

1. 在本仓库新增或修改 `.mdx`
2. 更新对应目录的 `_meta.json`
3. 提交并合并变更
4. docs-site 读取新内容并完成构建

## 推荐协作习惯

- 先定目录和 slug，再写内容
- 先补 `_meta.json`，再补页面文件
- 中英文路径保持一致
- 图片统一放到约定目录
- 改 slug 前先确认是否会影响外部链接
- 尽量通过 PR 合并，不直接改 `main`
