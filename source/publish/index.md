---
title: 发布文章
date: 2026-08-20 16:30:00
type: 'publish'
description: 在线发布博客文章
---

<div style="max-width: 760px; margin: 0 auto; padding: 20px 0 40px;">

<h2>在线发布</h2>

<p>点击下方按钮跳转到 GitHub 在线编辑器，在网页上直接写文章并提交，提交后会自动触发部署上线。</p>

<p style="text-align: center; margin: 30px 0;">
  <a href="https://github.com/SuperCoder07/SuperCoder07.github.io/new/main/source/_posts" target="_blank" style="display: inline-block; padding: 12px 36px; background: #425aef; color: #fff; border-radius: 8px; text-decoration: none; font-size: 16px; font-weight: bold;">
    打开 GitHub 编辑器
  </a>
</p>

<h3>使用步骤</h3>

<p>1. 点击上面的按钮，进入 GitHub 新建文件页面</p>
<p>2. 在文件名输入框填入 <code>source/_posts/你的文章名.md</code></p>
<p>3. 在编辑区粘贴下面的模板，然后写正文</p>
<p>4. 拉到底部点 <code>Commit new file</code> 提交</p>
<p>5. 等待 1-2 分钟，GitHub Actions 自动构建部署完成，文章即上线</p>

<h3>文章模板</h3>

<p>把下面这段复制到编辑区，替换其中的内容：</p>

<pre style="background: #f6f8fa; padding: 16px; border-radius: 8px; overflow-x: auto; font-size: 13px; line-height: 1.6;"><code>---
title: 文章标题
date: 2026-08-20 16:00:00
author: winner
categories:
  - 技术笔记
tags:
  - 标签1
  - 标签2
description: 文章摘要
cover: https://picsum.photos/800/450?random=1
---

在这里写正文内容，使用 Markdown 语法。</code></pre>

<h3>字段说明</h3>

<p><strong>title</strong>：文章标题<br>
<strong>date</strong>：发布时间<br>
<strong>author</strong>：作者，已默认填 winner<br>
<strong>categories</strong>：分类，只能填一个<br>
<strong>tags</strong>：标签，可以多个<br>
<strong>description</strong>：文章摘要<br>
<strong>cover</strong>：封面图链接，留空则用默认随机封面</p>

</div>
