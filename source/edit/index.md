---
title: 编辑文章
date: 2026-08-20 19:00:00
type: 'edit'
description: 在线编辑已有博客文章
---

<style>
.edit-wrap { max-width: 860px; margin: 0 auto; padding: 10px 0 50px; }
.edit-search { display: flex; gap: 12px; margin: 24px 0; align-items: center; }
.edit-search input {
  flex: 1; padding: 10px 16px; border: 1px solid var(--card-border, #e0e0e0);
  border-radius: 8px; font-size: 14px; background: var(--card-bg, #fff); color: var(--font-color, #333);
  outline: none; transition: border-color .2s;
}
.edit-search input:focus { border-color: #425aef; }
.edit-search .cnt { white-space: nowrap; font-size: 13px; color: var(--font-color, #666); opacity: .7; }
.edit-list { list-style: none; padding: 0; margin: 0; }
.edit-item {
  display: flex; align-items: center; justify-content: space-between; gap: 16px;
  padding: 16px 20px; margin-bottom: 10px; border-radius: 10px;
  background: var(--card-bg, #fff); border: 1px solid var(--card-border, #eee);
  transition: box-shadow .2s, transform .15s;
}
.edit-item:hover { box-shadow: 0 4px 16px rgba(0,0,0,.08); transform: translateY(-1px); }
.edit-item-info { flex: 1; min-width: 0; }
.edit-item-title { font-size: 16px; font-weight: 600; color: var(--font-color, #333); margin: 0 0 4px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.edit-item-meta { font-size: 12px; color: var(--font-color, #999); opacity: .7; }
.edit-btn {
  display: inline-flex; align-items: center; gap: 6px; padding: 8px 20px;
  background: #425aef; color: #fff !important; border-radius: 8px; text-decoration: none;
  font-size: 13px; font-weight: 600; white-space: nowrap; transition: background .2s;
}
.edit-btn:hover { background: #354fd1; color: #fff; }
.edit-empty { text-align: center; padding: 60px 20px; color: var(--font-color, #999); font-size: 14px; }
.edit-loading { text-align: center; padding: 40px; color: var(--font-color, #999); font-size: 14px; }
.edit-loading .dot { animation: pulse 1.2s infinite; }
@keyframes pulse { 0%,100% { opacity: .3 } 50% { opacity: 1 } }
.edit-refresh { margin-left: auto; padding: 8px 18px; background: var(--second-bg-color, #f5f5f5); color: var(--font-color, #555); border: 1px solid var(--card-border, #ddd); border-radius: 8px; cursor: pointer; font-size: 13px; transition: background .2s; }
.edit-refresh:hover { background: var(--third-bg-color, #eee); }
</style>

<div class="edit-wrap">
<h2>编辑已有文章</h2>
<p>下方列出仓库中所有文章，点击"编辑"按钮跳转到 GitHub 在线编辑器，修改后提交即可自动触发部署更新。</p>

<div class="edit-search">
  <input type="text" id="editFilter" placeholder="搜索文章标题或文件名..." oninput="filterPosts()">
  <span class="cnt" id="editCount"></span>
  <button class="edit-refresh" onclick="loadPosts()">刷新列表</button>
</div>

<div id="editStatus" class="edit-loading"><span class="dot">正在加载文章列表...</span></div>
<ul class="edit-list" id="editList" style="display:none"></ul>

</div>

<script>
var REPO = 'SuperCoder07/SuperCoder07.github.io';
var BRANCH = 'main';
var POSTS_PATH = 'source/_posts';
var postData = [];

function formatTime(bytes) {
  return bytes > 1024 ? (bytes/1024).toFixed(1) + ' KB' : bytes + ' B';
}

function titleFromName(name) {
  return name.replace(/\.md$/i, '').replace(/^[0-9]{4}-[0-9]{2}-[0-9]{2}-/, '').replace(/-/g, ' ');
}

function loadPosts() {
  var status = document.getElementById('editStatus');
  var list = document.getElementById('editList');
  status.style.display = 'block';
  status.innerHTML = '<span class="dot">正在加载文章列表...</span>';
  list.style.display = 'none';

  fetch('https://api.github.com/repos/' + REPO + '/contents/' + POSTS_PATH + '?ref=' + BRANCH, { headers: { 'Accept': 'application/vnd.github.v3+json' } })
    .then(function(r) {
      if (!r.ok) throw new Error('HTTP ' + r.status);
      return r.json();
    })
    .then(function(data) {
      postData = data.filter(function(f) { return f.type === 'file' && f.name.endsWith('.md'); });
      postData.sort(function(a, b) { return b.name.localeCompare(a.name); });
      renderList(postData);
    })
    .catch(function(err) {
      status.innerHTML = '加载失败：' + err.message + '<br><span style="font-size:12px;opacity:.6">可能是 GitHub API 限速，请稍后再试或点击刷新。</span>';
    });
}

function renderList(items) {
  var status = document.getElementById('editStatus');
  var list = document.getElementById('editList');
  var count = document.getElementById('editCount');

  if (items.length === 0) {
    status.style.display = 'block';
    status.className = 'edit-empty';
    status.innerHTML = '暂无文章';
    list.style.display = 'none';
    count.textContent = '';
    return;
  }

  status.style.display = 'none';
  list.style.display = 'block';
  count.textContent = items.length + ' 篇';

  list.innerHTML = items.map(function(f) {
    var title = titleFromName(f.name);
    var editUrl = 'https://github.com/' + REPO + '/edit/' + BRANCH + '/' + POSTS_PATH + '/' + encodeURIComponent(f.name);
    return '<li class="edit-item" data-title="' + title.toLowerCase() + '" data-name="' + f.name.toLowerCase() + '">' +
      '<div class="edit-item-info">' +
        '<p class="edit-item-title">' + title + '</p>' +
        '<span class="edit-item-meta">' + f.name + ' &middot; ' + formatTime(f.size) + '</span>' +
      '</div>' +
      '<a class="edit-btn" href="' + editUrl + '" target="_blank">' +
        '<i class="fas fa-pen"></i> 编辑' +
      '</a>' +
    '</li>';
  }).join('');
}

function filterPosts() {
  var q = document.getElementById('editFilter').value.trim().toLowerCase();
  if (!q) { renderList(postData); return; }
  var filtered = postData.filter(function(f) {
    var title = titleFromName(f.name).toLowerCase();
    return title.indexOf(q) !== -1 || f.name.toLowerCase().indexOf(q) !== -1;
  });
  renderList(filtered);
}

// auto load on page ready
if (document.readyState !== 'loading') { loadPosts(); }
else { document.addEventListener('DOMContentLoaded', loadPosts); }
</script>
