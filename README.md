# blog-leo
### 基本介绍
Hexo+Butterfly搭建的个人博客

每次新增/修改博客后
```shell
hexo clean && hexo generate && hexo algolia && hexo deploy
```
注意
由于hexo-theme-butterfly是通过Npm进行引入的。目前魔改变动大的话也会修改npm包中的内容。当下先做好记录，之后可以采取patch-package方式解决
- hexo-theme-butterfly\layout\includes\header\social.pug内容被替换
- hexo-theme-butterfly\layout\includes\header\nav.pug 第14行被注释
- hexo-theme-butterfly\layout\includes\head.pug  新增第70行
- hexo-theme-butterfly\source\css\和  ...\js\分别新增cat.css和cat.js