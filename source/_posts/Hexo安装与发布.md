---
title: Hexo安装与发布
categories: 工具&配置
---
## 安装
1. 安装hexo
> `npm install hexo-cli -g`

2. 初始化hexo
> `hexo init <文件名>`

3. 打开文件
> `cd <文件名>`

4. 安装node_modules
> `npm install`

5. 启动hexo服务，启动后可通过`http://localhost:4000/`就可以看到博客啦~
> `hexo server`

## 发布
1. github新建一个项目（start a project）,新建分支，如hexo,打开settings，将repository name命名为`XXX.github.io`,该仓库可以被发布到`https://XXX.github.io`

2. 打开站点的配置文件_config.yml，修改为:

  ```
type: git
repo: https://github.com/guoyueting/guoyueting.github.io
branch: master
  ```
  deploy的配置是让hexo知道你要把blog部署在哪个位置，因此hexo分支可以保留当前源代码，master分支则用来发布的html等文件。

3. 为了使用hexo d来部署到git上，需要先安装Git部署插件。
> `npm install hexo-deployer-git --save`


4. 清除旧public文件
> `hexo clean`

5. 生成并发布html，本地生成不用加-d
> `hexo g -d`








