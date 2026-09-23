---
title: 从零搭建这个网站
date: 2026-09-23 20:00:00
updated: 2026-09-23 20:00:00
categories:
  - 建站
tags:
  - Hexo
  - Butterfly
  - Vercel
  - 域名解析
description: 记录 liusep.cn 从买域名到上线的完整过程，包括踩过的坑和最终的技术选型。
cover: /img/bg-star.jpg
top_img: /img/bg-star.jpg
---

## 为什么建这个站

用公开输出来督促自己持续输入。把学到的写下来，才知道自己到底懂没懂。

这篇是第一篇，记录建站本身的过程 —— 顺便当作 Stack 的笔记。

## 技术选型

| 环节 | 选择 | 理由 |
|---|---|---|
| 静态站点框架 | Hexo 7.3.0 | 生态成熟，Markdown 写作体验好 |
| 主题 | Butterfly 5.7.0 | 卡片式布局，配置项丰富，中文文档完善 |
| 托管 | Vercel Hobby | 免费、自动 HTTPS、推送即部署 |
| 域名 | liusep.cn | 腾讯云注册 |

## 部署链路

整条链路是：

```
本地 Markdown 写作
      ↓ git push
   GitHub 仓库
      ↓ 自动触发
  Vercel 构建（hexo generate）
      ↓ 产物发布
  liusep.cn（DNSPod 解析）
```

> [!NOTE]
> 关键点：Vercel 需要在仓库里有构建配置才知道要跑 `hexo generate`，否则会把它当普通静态站处理，构建产物是空的。

## 踩过的坑

### 1. 忘了配 DNS 解析

域名审核通过 ≠ 可以访问。审核只代表域名激活，还需要去 DNSPod 添加解析记录指向 Vercel，否则解析记录里只有 SOA，没有 A/CNAME。

### 2. 站点 url 没改

`_config.yml` 里 `url` 默认是 `http://example.com`。Hexo 用它生成所有绝对路径，不改的话站内链接、RSS、站点地图全都会指向错误地址。

### 3. 主题装在 node_modules 里

Butterfly 通过 npm 安装，`themes/` 目录是空的。这意味着**不能直接改主题文件**（会被 npm 覆盖），要改样式得走官方覆盖机制。

## 接下来

- [ ] 域名解析生效
- [ ] 加入自定义动效
- [ ] 持续更新内容

---

*持续更新中。*
