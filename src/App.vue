<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <!-- '/about'リンクを削除し、'/todo-list'のリンク追加 -->
      <router-link to="/todo-list">TodoList</router-link> |
      <router-link to="/Issue-list">IssueList</router-link> |
      <router-link to="/TodosIssues">Todos-Issues</router-link>
    </div>
    <router-view/>
  </div>
</template>

<!-- 以下変更なし -->

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
