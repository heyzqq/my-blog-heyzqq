# heyzqq.github.io

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

   # 本地部署
   $ hexo clean
   $ hexo generate (g)
   $ hexo server (s)
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

#### 4. 包管理

同步库的时候, 需要运行 `npm install`(或`yarn install`), 以便将需要的包安装进来.  
`npm install`, `npm install --save` 与 `npm install --save-dev`:  

- ① 会把包安装到 `node_modules` 中; 不修改 `package.json`; 之后运行 `npm install` 不会自动安装该包.  
- ② 会把包安装到 `node_modules` 中; 在 `package.json` 中的 `dependencies` 位置添加属性; 会自动安装该包到 `node_modules` 目录中.  
- ③ 会把包安装到 `node_modules` 中; 在 `package.json` 中的 `devDependencies` 的位置添加属性; 会自动安装该包到 `node_modules` 目录中.  
    运行 `npm install --production` 或者注明变量值 `NODE_ENV` 为 `production` 时, 不会自动安装.  
* 总之, 运行时需要的用到的包使用 `--save`, 否则使用 `--save-dev`  


