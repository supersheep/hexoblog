---
layout: post
title: 介绍一下Grunt
date: 2013-03-23 19:40:48
comments: true
tags:
---

Grunt是一枚基于Task的静态文件打包工具。我们完成开发要上线之前，需要对js,css进行检查，跑单元测试，然后编译，压缩，合并这些事情，以节省最终用户的加载时间。这样的事情自然可以由简单的shell脚本来进行，但是项目多了跨度大了，就希望规则方便编写，易于维护，这个工具可以跨平台blahblahblah……

来看下怎么用：

1. 首先总得有命令行工具吧。

```
npm install -g grunt-cli
```

2. 在package.json里面加好devDependencies

```
npm install grunt --save-dev // grunt runner
npm install grunt-contrib-uglify --save-dev
```

3. 编写 gruntFile.js

``` javascript
module.exports = function(grunt) {

  // 配置任务
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n' // 支持写模板
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js'
      }
    }
  });

  // 加载uglify task，我们刚才装好的
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // 注册默认任务，它包括一个uglify子任务
  grunt.registerTask('default', ['uglify']);

};
```

这样别人在拿到你的源码之后只需要

```
npm install // 安装包括grunt在内的各种依赖
grunt // 构建项目
```

就可以了，非常方便。自己的构建任务，依赖自己知道，别人拿到手不需要任何额外配置，可以很傻瓜的进行构建。

因为之前自己在公司也花精力做过这样的尝试，所以看人家的做法就会跟自己做过的相比较。来讲几个设计上我觉得比较亮眼的地方。

1. 工具函数都丢在grunt对象上

这样在编写插件的时候可以很方便的利用这些函数，其实是很基本大家都很有共识的做法了，不过有时候还是会忘了这招。

2. 配置写成js脚本而非json数据

这样对一些比较复杂的操作就比较游刃有余，比如源文件到目标文件可能需要一个函数来映射，只有json是搞不定的。

3. 方便的文件收集

通过识别配置中是否包含src、dest或者files来收集需要处理的文件，可以在自定义的taskFunction中通过this.files拿到，支持wildcard。

4. 自定义task接口

```
grunt.registerTask(taskName, [description, ] taskFunction)
grunt.registerMultiTask(taskName, [description, ] taskFunction)
```

相信稍大点的项目都有类似的机制，我想说的是优先级，应该排到非常前面，并且有必要放到项目首页显眼的位置。

做核心，做接口，让社区来做贡献，可以起到四两拨千斤的功效。今天下午看到[这个页面](http://gruntjs.com/plugins)的时候就哭出来了。提供可能性，然后功能的丰富只是时间问题。

实际上grunt的插件机制说白了也就是通过loadNpmTasks去node_modules里面找对应的插件文件夹下面的tasks目录中的js，进而调用以上两个api来注册task。

再说一个不置可否的功能，就是watch了。功能上就是观察源码是否被改动过，若有改动则自动触发构建任务。很多工具都提供了这样的功能，不过就generating这点来讲我觉得很不环保。像我这样平时习惯了敲一句话按一下command+s的，难道要每一下都把所有文件都重新打一遍么。这种事情由本地静态服务做掉就ok了，访问的时候才生成需要生成的内容。进入排错阶段，自动触发集成测试的话还是比较有意义的。

看过之后感觉不错，而且各种插件又大家都在用，比较容易暴露问题被修复掉。思考自己之前做的东西，是否需要废弃掉全部切换过来，想想倒是不必，这个问题其实有点类似于业务没变的时候是否需要做框架迁移。工程师比较容易产生这种形式上的洁癖，其实意义不大。过去的东西虽有瑕疵但运行得没什么大问题就让他去好了，不过要看到这些缺陷，看看别人是怎么做的，然后把更好的做法运用到今后的项目当中去。

