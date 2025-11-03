<template>
  <main class="container">
    <h1><img src="/logo.png" class="logo" /> 我的书签</h1>
    
    <div v-if="loading" class="loading">
      正在加载书签...
    </div>

    <div v-else-if="bookmarkRoot" class="bookmark-grid">
      <BookmarkNode 
        v-for="node in bookmarkRoot.children" 
        :key="node.id" 
        :node="node" 
      />
    </div>
    
    <div v-else class="error">
      无法加载书签。请确保已授予书签权限。
    </div>
  </main>
</template>

<script>
import BookmarkNode from './components/BookmarkNode.vue';

export default {
  name: 'App',
  components: { BookmarkNode },
  data() {
    return {
      loading: true,
      bookmarkRoot: null // 整个书签树的根 (id='0')
    };
  },
  mounted() {
    // 检查 chrome API 是否存在，以防在普通浏览器中打开
    if (window.chrome && chrome.bookmarks) {
      chrome.bookmarks.getTree((tree) => {
        // getTree() 返回一个数组，其中 [0] 是根节点
        if (tree && tree.length > 0) {
          this.bookmarkRoot = tree[0];
        } else {
          console.error("未能获取书签树。");
        }
        this.loading = false;
      });
    } else {
      // 模拟数据 (用于在普通浏览器中调试UI)
      console.warn("Chrome Bookmarks API 不可用。正在使用模拟数据。");
      this.bookmarkRoot = {
        id: '0',
        children: [
          { id: '1', title: '书签栏 (模拟)', children: [
            { id: '3', title: 'Google', url: 'https://google.com' },
            { id: '4', title: 'Vue Docs', url: 'https://vuejs.org' },
          ]},
          { id: '2', title: '其他书签 (模拟)', children: [
            { id: '5', title: 'Vite', url: 'https://vitejs.dev' },
          ]}
        ]
      };
      this.loading = false;
    }
  }
};
</script>

<style lang="scss">
/* 可以在 src/assets/base.css 或这里编写全局样式 */
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  background-color: #f4f7f6;
  color: #333;
  margin: 0;
  padding: 2em;
}

.container {
  max-width: 900px;
  margin: 0 auto;
}

h1 {
  color: #42b883; /* Vue 绿色 */
  display: flex;
  align-items: center;
  gap: 10px;
}

.logo {
  width: 32px;
  height: 32px;
}

.loading, .error {
  font-size: 1.2em;
  color: #777;
}

/* 我们使用 Grid 布局来展示顶层文件夹 */
.bookmark-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
}
</style>