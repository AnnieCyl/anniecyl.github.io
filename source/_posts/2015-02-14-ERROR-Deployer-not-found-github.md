title: "ERROR Deployer not found: github"
date: 2015-02-14 10:28:28
tags: 
- hexo
---

自从搭建了我的博客，2 个多月没再更新。结果今天想要发布博客的时候发现 `hexo g` 和 `hexo d` 居然不能用了，提示“ERROR Deployer not found: github”。

于是上网查了，原来是 hexo 版本升级以后 _config.yml 配置里面不再支持 github，要将 github 改成 git:
```
deploy:
  type: git
  repository: git@github.com:AnnieCyl/anniecyl.github.io.git
  branch: master
```


然后再安装一些新的插件：

### Modify package.json

Add `hexo` data to `package.json`.

```
    {
      "hexo": {
        "version": ""
      }
    }
```

### Clean cache

```
hexo clean
```

### Install generators

```
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
```

### Install server

```
npm install hexo-server --save
```

### Install deployers

```
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
```

### Update plugins

```
npm install hexo-renderer-marked@0.2 --save
npm install hexo-renderer-stylus@0.2 --save
npm install hexo-generator-feed@1 --save
npm install hexo-generator-sitemap@1 --save
```

<https://github.com/hexojs/hexo/wiki/Migrating-from-2.x-to-3.0#modify-packagejson>