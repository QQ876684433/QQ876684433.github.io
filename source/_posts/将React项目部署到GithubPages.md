---
title: 将React项目部署到GithubPages
date: 2019-07-14 22:50:59
categories:
- 项目
tags:
- React
- 项目
---

### 将React项目部署到Github Pages

> 参考：https://segmentfault.com/a/1190000019290048

一些注意事项：

（1）Github Pages自定义域名问题

- 往gh-pages分支添加CNAME记录

  ![](https://i.loli.net/2019/07/04/5d1e10ad9b6d873438.png)

- 将要添加的域名写入CNAME，但是注意不要加入`http://`

  ![](https://i.loli.net/2019/07/04/5d1e110ab54e333768.png)

- 这里还有个问题就是，由于gh-pages分支是将编译之后的内容上传的，如果直接在gh-pages分支添加CNAME文件，会导致出现重新部署的时候CNAME被删除，这时后如果再次访问自定义域名，就会出现Content Security Policy问题（参见：https://stackoverflow.com/questions/54380373/content-security-policy-while-deploying-to-github-pages-using-gatsby）

  ```cmd
  Refused to load the image 'http://www.alexingberg.com/favicon.ico' because it violates the following Content Security Policy directive: "img-src data:".
  
  ```

  你可以按照stackoverflow上面的方案解决（我没试过），但我这里提供一个简单的解决方案，就是将CNAME添加在项目的public文件夹下面，编译之后CNAME就会在build目录生成，这样就不会出现Content Security Policy问题了

  ![](https://i.loli.net/2019/07/04/5d1e126fab25166732.png)

（2）自定义域名后出现资源路径不正确的问题

- 网上一些教程都是教你把Github Pages生成的那一串链接地址设置到`"homepages"`中

  ![](https://i.loli.net/2019/07/04/5d1e1359bcf0850375.png)

  这样会出现的问题就是，编译完成后，会在静态资源的路径前面都加上`/chph-blog`，这样肯定是找不到对应的资源的

- 可以通过把`"homepages"`设置成`"./"`即可

  ![](https://i.loli.net/2019/07/04/5d1e13f324be160749.png)