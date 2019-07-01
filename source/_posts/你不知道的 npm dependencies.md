---
title: 你不知道的 npm dependencies
tag: npm dependencies
date: 2019-06-30
categories:
- 前端
toc: true
---

今天中午同事小明突然问 package.json 中的 dependencies 和 devDependencies 有什么区别吗？

听到这个问题我就很自然而然的告诉他 dependencies 是生产的依赖包，devDependencies 是开发时需要的依赖包。

然而小明同学对这个答案并不满意，继续说道：“我刚刚试了一下，我把生产的依赖包安装在 devDependencies，然后用 webbpack 还是会把 devDependencies 中的生产依赖包打包进去”。

听到这里我内心产生了一丝慌乱，难道我对 dependencies 和 devDependencies 理解有误，我立即以迅雷不及掩耳盗铃之势打开了 Chrome 浏览器，噼里啪啦一顿操作打开了 [https://docs.npmjs.com/cli/install](https://docs.npmjs.com/cli/install) 。

### npm install 是什么？
首先了解一下什么是 npm install: 这个命令就是安装程序所依赖的第三方包，如果直接执行 npm install，会根据 package.json 中依赖的第三方包全部安装到 node_modules 这个文件夹下，并且会产生一个 package-lock.json 文件，下次再次执行 npm install 时会根据 package-lock.json 中的包已经安装。

如果安装指定依赖包直接在 npm install 后面跟上依赖包的名称即可。除了可以在 npm 仓库安装 npm 包之外，我们也可以直接在本地安装依赖包或者通过 url 安装依赖包。
- 本地安装依赖包
> npm install ./package.tgz

- 通过 url 安装依赖包，为了和其他的安装方式进行区分 npm install 后面的参考必须以 "http://" 或 "https://" 开头
> npm install https://github.com/indexzero/forever/tarball/v0.5.6

在安装 npm 包的时候我们可以通过添加第二个参数指定当前的安装的依赖包安装在 devDependencies 或者 dependencies 中。

如果 npm install 不加第二个参数则默认保存到 dependencies 中。下面对第二个参数进行简单的介绍。
| 命令 | 缩写 | 说明 |
| --- | --- | --- |
| --save-prod | -P | 保存到 dependencies 中 |
| --save-dev | -D | 保存到 devDependencies 中 |
| --no-save  | - | 不保存到 dependencies 中 |

### dependencies 和 devDependencies
说了这么多还是没有解决小明同学的问题，小明同学的问题该怎么回答呢？其实官网上已经解答了这个问题。
![-w703](http://image.volc.top/2019-07-01-15619530639128.jpg?attname=&e=1561961265&token=3hpwDXSbS4xMwQjV2bYCDWiaevTvbbrJiT0G0cWf:SB-sHIKIr8IDtA1wViVDWfXp-jc)

> npm install --production
执行上面的命令时安装的是 dependencies 的依赖，不添加 `--production` 则安装所有的依赖包。

dependencies 和 devDependencies 中的包和 webpack 没有必要的联系，webpack 会根据是否在项目中使用去打包依赖包，所以安装在 devDependencies 中的依赖包也会被打包进去。

#### dependencies 和 devDependencies 有什么作用
既然安装在 dependencies 和 devDependencies 中 webpack 都会打包使用的依赖项，那安装在 dependencies 和 devDependencies 中还有什么区别呢？

使用 dependencies 和 devDependencies 区别开发和生产的依赖包，可以让别人能够快速的通过 package.json 中了解到整个项目的依赖情况，让项目的对于第三方的依赖更加的清晰。

还有在安装第三方依赖包的时候 npm 不会安装 devDependencies 中的依赖包，所以如果你想发布一个 npm 包需要搞清楚项目中有哪些是必须依赖的第三方包。

### 总结
目前为止我们已经解决了小明同学的疑问，dependencies 和 devDependencies 是 npm 的依赖包，和 webpack 打包没有必然的联系，webpack 打包只根据项目是否引用了包。

dependencies 和 devDependencies 可以帮助我们更清晰的了解到整个项目所依赖的第三方包。
对于已发布在 npm 仓库中的包，我们在安装依赖的时候只会安装 dependencies 中依赖的包。

