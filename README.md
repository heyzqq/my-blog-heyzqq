# TaylorBoy.github.io

#### 1. 环境搭建

- Node.js
- Git
  ```
  $ npm install -g hexo-cli
  ```

#### 2. 初始化

  ```
  $ hexo init <folder>
  $ cd <folder>
  $ npm install (或 yarn install)
  ```

#### 3. 发布

1. 创建文档
   ```
   $ hexo new [layout] <title>
   # 布局  路径
   # post  source/_posts
   # page  source
   # draft source/_drafts
   ```
2. 配置 git (_config.yml)
   ```
   # Deployment
   ## Docs: https://hexo.io/docs/deployment.html
   deploy:
     type: git 
	 repository: git@github.com:TaylorBoy/TaylorBoy.github.io.git
	 branch: master
   ```
3. 生成并发布(发布的是生成的public目录)
   ```
   # 在生成以及部署文章之前，需要安装一个扩展：
   $ npm install hexo-deployer-git --save

   # 生成 & 发布
   $ hexo d -g
   ```
4. 删除文档
   ```
   # 先添加一个文件，再删除。_post 文件夹不能为空
   $ rm *.md
   $ hexo d -g
   ```
5. leancloud 阅读统计:  
   再修改文章后, 若不能正常访问服务器, 可以删除 `<<blog directory>>/source/leancloud.memo`,  
   然后重新部署: `hexo d`

