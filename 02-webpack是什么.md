# webpack是什么？

Webpack是一款模块打包工具，你可以用它来帮助你打包。但是随着社区的发展，很多开发插件的涌现，这种定义越发模糊起来。一些插件所做的功能任务已经远远超出了打包的范畴，举例来说，有些插件可以帮助我们清理打包目录，还有的插件可以帮助我们处理打包后文件的线上部署。

Webpack在React中非常的受青睐，因为它支持热模块替换（Hot Module Replacement），这也使得webpack迅速变的家喻户晓，甚至扩展到了其他语言的开发中，比如Ruby On Rails。看到webpack中的web这个单词，你可能觉得webpack只能应用于网页开发之中，可不是这样的！webpack可以打包其他的资源，这部分的内容我们将会在 “构建目标文件” 小节中详述。

读到这，如果你恰好对React有所了解，却又不知道Hot Module Replacement(HMR) 为何物，我建议大家先看一下HMR的基本介绍，之后看下这篇文章：[点击阅读](https://segmentfault.com/a/1190000006178770)，记录好如何配置HMR即可，接下来的教程中，我们可能继续给大家扩展HMR的相关知识。

## Webpack的打包建立在模块基础上

当你的项目中配置了webpack的**入口(input)**和**出口(output)**后，就可以使用webpack打包了。打包的过程从你的入口文件开始，入口文件就是一个模块（modules），通过imports可以引入其他模块。

当使用webpack打包项目时，它首先会便利模块引用（imports），建立一个项目的依赖关系图(dependency graph)，然后根据这个关系图和你的配置文件内容，生成你所需要的输出文件。你可以在代码中设置切分点(split point)，webpack可以根据设置，把工程的代码切分成多个。注意，webpack分析依赖关系图的时候，会通过一些静态的方式生成，而不是靠动态的运行文件来分析，这样性能上的消耗很小。

Webpack 支持ES6，CommonJS, AMD 的模块化方案.内部加载器(loader)机制使得它也可以处理css文件间的依赖关系，你只要安装css-loader后使用@import和url()即可完成css间的相互引用。你还可以使用很多Webpack的插件来帮助你完成指定的任务，比如说压缩代码，代码国际化，热模块加载等等。

# Webpack执行流程

![webpack流程](https://github.com/shenglongli/webpack-book/blob/master/imgs/webpack-process.png)

Webpack从入口开始，这些入口一般会引入很多其他的模块，webpack遍历这些模块，开始打包流程。过程中，webpack会根据文件的类型，和对应的loader进行匹配，然后根据匹配结果执行对应的转换操作。

## 解析过程

入口本身就是一个模块，webpack遇到一个模块时，他会根据文件中的依赖去找寻这个模块所需的所有内容。

如果解析失败，webpack会给出运行时错误。如果解析成功，webpack就可以根据loader和其他配置项的定义运行打包流程了。每一个loader在模块内容打包时都负责处理指定部分内容的转换。

对于一个loader来说，它到底要负责哪部分内容的转化呢？定义loader职责的方法有多种，你可以根据文件的类型来决定使用哪个loader，也可以根据文件所在的路径来执行对应的loader。webpack还提供了更加灵活的方式，你甚至可以根据文件在哪里被引用来决定使用什么loader进行解析，怎么样，很酷吧！

解析的过程中，loader模块中的内容也会被解析。loader也可以被其他loader所解析，他们也有自己的配置文件，总之，一旦loader解析没通过，你的webpack一样会报错。

## webpack几乎可以处理任何文件类型

Webpack 会处理构建过程中依赖图谱中的所有模块。如果入口包含任何依赖，每个依赖的模块都会被处理到。无论什么文件类型，webpack都不会放过，这一点不像Babel或者Sass的编译器，他们只负责处理固定的文件类型。

你可以控制webpack遇到不同文件时作出的不同相应。你可以让webpack把某些静态资源打包到js里，这样可以避免额外的网络请求。webpack还提供了一些使用技巧，你可以通过webpack实现css的模块化，避免像以前一样的非模块化css编码。这些webpack带来的灵活性，使得webpack具有非常大的使用价值。

尽管webpack主要被用做打包js，它也可以捕获图片或者字体这样的静态资源并对它们进行单独处理。入口只是webpack构建过程中的开始执行点，具体打包过程时如何的，完全依赖于你怎么做的打包配置。

## 执行流程

webpack会先加载解析模块的所有loader，然后按照从下至上，从右到左的顺序执行（这点非常重要），如果你希望实现这样的效果：styleLoader(cssLoader('./main.css'))，先解析css再挂载到页面，那么顺序一定要放对。加载其定义的章节会详细阐述这部分的内容。

如果执行过程中没有出现任何的错误，webpack会把资源打包到最后生成的文件中。插件的作用在于，可以在构建流程中的某些具体时刻去做一些运行时插入的操作。

尽管加载器（loader）可以做很多操作，在高级任务中，他们能做的事情也有限。webpack内部提供了插件机制，可以在运行时执行一些插入事件，比较好的一个例子是：ExtractTextPlugin这个插件可以帮助我们在打包过程中抽离出独立的css文件，这个插件和loader是一个串联的运行关系。如果不使用这个插件，css和js会被打包在一起，关于抽取这个概念，在css分离一章中我们会具体讲解。

## 打包结束

当所有相互引用的模块被处理后，webpack会生成输出文件。输出文件中会抱憾一个启动脚本，指引浏览器如何执行这段代码。你也可以把这个脚本单独的生成为一个文件，这个后续我们会深入讨论。输出文件的结果你可以根据环境的不同作不同的配置，你可以生成测试环境的代码，生成线上环境的代码，你也可以生成非web项目的一些代码。

上述的内容并不是构建过程的全部。例如你可以定义文件的切分点，根据业务逻辑载入不同的切割文件，这部分的内容在代码分割章节会被讲到。

# Webpack 是配置驱动的

webpack运行的核心是基于配置的。这是官方教程中的一个配置demo，它能够带你简单的了解webpack的基础配置。

#### webpack.config.js

``` javascript

const webpack = require('webpack');

module.exports = {
  // 打包入口
  entry: {
    app: './entry.js',
  },

  // 输出目录
  output: {
    // 输出文件所在目录
    path: __dirname,

    // 根据模式匹配出打包生成文件的名字
    filename: '[name].js',
  },

  // 当遇到imports语句时，如何进行解析
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
      {
        test: /\.js$/,
        use: 'babel-loader',
        exclude: /node_modules/,
      },
    ],
  },

  // 附加要处理的内容
  plugins: [
    new webpack.optimize.UglifyJsPlugin(),
  ],

  // 适配模块解析的一些算法和配置
  resolve: {
    alias: { ... },
  },
};
```

当webpack的配置文件比较庞大复杂的时候，这种配置模式看起来会让人比较难理解。如果你不了解底层的解析机制，你是看不懂这些配置文件的，这个教程的目的就是一步步带大家了解原理，帮助你学会如何配置webpack。

## 热模块替换（Hot Module Replacement）

你可能对LiveReload，BrowserSync这种工具很熟悉了，当你更改代码的时候，这些工具会帮助你自动刷新页面。HMR比这些工具要更高级一点。在React中，如果你使用HMR，可以在不改变组件state的情况下完成页面的刷新。使用livereactload在Browserify中也可以实现HMR，所以这个热模块加载不是专属webpack的。

## 代码分割

除了HMR特性，webpack打包的能力还有很多，它允许你通过不同的形式来拆飞打包代码。你甚至可以设置在应用运行的时候动态的加载执行代码。这些懒加载的方式使用起来也很简单，在大型项目中会被经常使用。当需要使用它们时，你只需加载相应的依赖即可。

小型项目也可以通过代码分割受益，它能够让你的应用访问速度更快，性能更好。所以花些了解这方面的知识也是值得的。

## 给资源添加哈希值

使用webpack你可以给每一个打包文件后面加一个hash后缀，每当代码变化，哈希值都会跟着变。这使得页面代码变化时，如果你使用代码分割的功能，那么每次修改局部代码时，你只需要重新加载很少量的代码。

## 总结

webpack学习的曲线非常陡峭，但是它确实是值得你学习的，在长期的使用学习过程中，你会发现它会帮你节约大量的时间和生产力。关于webpack和其他工具的一些比较，官网上可以找到详细的介绍。

webpack不能帮你解决所有的问题，但是它可以帮你解决开发过程中令人挠头的打包问题。使用package.json和webpack，仅仅这两个东西就可以帮你大忙。

# 章节小节:

webpack是一个模块打包工具，但你也可以使用它作为任务构建工具。

webpack底层依赖于模块间的关系图谱，webpack根据配置文件，遍历模块来构建这个图谱，并根据这个图谱来创建打包文件。webpack也依赖于内部的加载器（loader）和插件（plugin）。loader在模块级别进行操作处理，而插件依赖于webpack提供的钩子在具体的时间点执行，插件可以在丰富的时间点对打包流程进行处理。

webpack的配置文件描述了如何转换过程打包文件中的的各类文件，如何最终生成打包文件等内容。有时候，在源代码里也会有一些配置信息，比如说你希望使用代码分割的功能，那么在业务代码中，你也需要有一些配置内容。

热模块替换功能(HMR)使得webpack越发流行，这个特性能够提供用户更加的开发体验，不需要刷新页面，开发者就可以更新页面的内容。webpack可以自动增加文件哈希后缀，方便代码变更后上线，解决用户缓存问题。

教程的下一部分，你将会学会如何配置webpack的配置文件，同时了解更多的webpack基础概念。










