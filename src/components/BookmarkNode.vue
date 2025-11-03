<template>
  <div class="bookmark-node">
    
    <div v-if="node.children && node.children.length > 0" class="folder">
      <strong class="folder-title">{{ node.title }}</strong>
      <div class="folder-content">
        <BookmarkNode 
          v-for="child in node.children" 
          :key="child.id" 
          :node="child" 
        />
      </div>
    </div>

    <a v-else-if="node.url" :href="node.url" class="bookmark-link" target="_blank" rel="noopener noreferrer">
      <img :src="getFaviconUrl(node.url)" class="favicon" @error="onFaviconError" />
      <span class="bookmark-title">{{ node.title || '无标题' }}</span>
    </a>

    <div v-else-if="node.children" class="folder empty-folder">
      <span class="folder-title">{{ node.title }} (空)</span>
    </div>

  </div>
</template>

<script>
export default {
  name: 'BookmarkNode',
  // 通过 props 接收节点对象
  props: {
    node: {
      type: Object,
      required: true
    }
  },
  methods: {
    // 辅助函数：获取 Favicon
    getFaviconUrl(url) {
      // 使用 'chrome://favicon/' API, 必须在 manifest.json 中声明 "favicon" 权限
      // 这是最高效、最安全的方式
      return `chrome://favicon/size/16/${url}`;
    },
    // Favicon 加载失败时的备用方案
    onFaviconError(event) {
      // 替换为通用的 "地球" 或 "书签" 图标
      event.target.src = 'data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0iY3VycmVudENvbG9yIiB3aWR0aD0iMTYiIGhlaWdodD0iMTYiPjxwYXRoIGQ9Ik0xMiAyQzEwLjkgMiAxMCAyLjkgMTAgNFYxMEgyVjEySDNWMTNDMyAxNy40MiA2LjU4IDIxIDEgMjFIMTJDMTcuNDIgMjEgMjEgMTcuNDIgMjEgMTNWMTJDMjEgMTAuOSAyMC4xIDEwIDE5IDEwSDE0VjRDMTQgMi45IDEzLjEgMiAxMiAyWk0xOSAxM1YyMEg1VjEzSDE5Wk0xMiA2SDIyVjhIMTJWNloiLz48L3N2Zz4=';
    }
  }
};
</script>

<style lang="scss" scoped>
/* 文件夹样式 */
.folder {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  padding: 12px;
  /* 确保文件夹可以嵌套 */
  width: 100%;
  box-sizing: border-box; /* 重要 */
}

.folder-title {
  font-size: 1.1em;
  color: #34495e;
  display: block;
  margin-bottom: 8px;
  border-bottom: 1px solid #eee;
  padding-bottom: 8px;
}

.folder-content {
  display: flex;
  flex-direction: column;
  gap: 5px; /* 书签之间的间距 */
}

.empty-folder .folder-title {
  color: #999;
  font-style: italic;
  border-bottom: none;
}

/* 书签链接样式 */
.bookmark-link {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 6px 8px;
  border-radius: 5px;
  text-decoration: none;
  color: #2c3e50;
  transition: background-color 0.2s ease;
  
  /* 防止长标题换行 */
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.bookmark-link:hover {
  background-color: #ecf0f1;
}

.favicon {
  width: 16px;
  height: 16px;
  flex-shrink: 0; /* 防止图标被压缩 */
}

.bookmark-title {
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>