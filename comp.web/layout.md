## Layout

DIV+CSS规范命名
* 外套 wrap：用于最外层
* 头部 header： 用于头部
* 主要内容 main： 用于主体内容（中部）
* 左侧 main-left： 左侧布局
* 右侧 main-right： 右侧布局
* 导航条 nav： 网页菜单导航条
* 内容 content： 用于网页中部主体
* 底部 footer： 用于底部


#### Bootstrap 实例

```
header#header.container>.row>.12u>h1.logo+nav.nav>a[#]{test}*5

.wrapper>header.main-header+aside.main-sidebar+.content-wrapper+footer.main-footter+aside.control-sidebar.control-sidebar-dark

<div class="wrapper">
  <header class="main-header"></header>
  <aside class="main-sidebar"></aside>
  <div class="content-wrapper"></div>
  <footer class="main-footter"></footer>
  <aside class="control-sidebar control-sidebar-dark"></aside>
</div>
```
