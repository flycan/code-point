# 快速开始

安装 `docsify-cli` 命令行工具

```bash
npm i docsify-cli -g
```

#### 初始化项目
```bash
docsify init ./docs
```
初始化成功后，可以看到 `./docs` 目录下创建的几个文件
- `index.html` 入口文件
- `README.md` 会做为主页内容渲染
- `.nojekyll` 用于阻止 `GitHub Pages` 会忽略掉下划线开头的文件

#### 预览网站
```bash
docsify serve docs
```
默认访问 [http://localhost:3000](http://localhost:3000)。

#### 显示目录
```html
<script>
  window.$docsify = {
    loadSidebar: true,
    subMaxLevel: 2
  }
</script>
<script src="//unpkg.com/docsify"></script>
````

#### 忽略副标题

当设置了 `subMaxLevel` 时，默认情况下每个标题都会自动添加到目录中。如果你想忽略特定的标题，可以给它添加  `{docsify-ignore}` 。

```markdown
# Getting Started

## Header {docsify-ignore}

该标题不会出现在侧边栏的目录中。
```

要忽略特定页面上的所有标题，你可以在页面的第一个标题上使用 `{docsify-ignore-all}` 。

```markdown
# Getting Started {docsify-ignore-all}

## Header

该标题不会出现在侧边栏的目录中。
```



#### 配置项
```js
auto2top: true, //切换页面后是否自动跳转到页面顶部。
coverpage: true, //激活封面功能。如果为true，则会从中加载_coverpage.md。
executeScript: true, //执行页面上的脚本。只解析第一个脚本标记（演示）。如果存在Vue，则默认开启。
loadSidebar: true, //_sidebar.md如果为真，则从_sidebar.md文件加载边栏，否则从指定的路径加载。
loadNavbar: true,//_navbar.md如果为真，则从_navbar.md文件加载navbar ，否则从指定的路径加载。
mergeNavbar: true,//Navbar将在小屏幕上与侧边栏合并。
maxLevel: 4,//最大的内容表级别。
subMaxLevel: 2,//在自定义边栏中添加目录。
ga:'UA-106147152-1', //谷歌统计 - Google Analytics 需要配置 track id 才能使用。
```

> [docsify 中文文档](https://docsify.js.org/#/zh-cn/)