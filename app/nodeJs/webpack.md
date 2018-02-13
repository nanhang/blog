## webpack 最基础的配置

1. webpack 概念

    webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle
    
    **总结: webpack 是一个可高度配置的静态模块打包器**
    
    * **webpack的工作原理**
    
        webpack处理文件的过程可以分为两个维度：文件间的关系和文件的内容
        
        文件间的关系:
        
            主要是通过文件和模块标记方法来实现
            
        文件的内容：
        
            文件内容的处理主要通过loaders和plugins来处理
            
        [webpack打包过程示意图]()
            
    * **与其他打包工具的异同**
    
        webpack 是一个模块打包器(module bundler)（例如，Browserify 或 Brunch）。它不是一个任务执行器(task runner)（例如，Make, Grunt 或者 Gulp ）。
        
        `任务执行器就是用来自动化处理常见的开发任务，例如项目的检查(lint)、构建(build)、测试(test)。`
    
        相对于打包器(bundler)，任务执行器则聚焦在偏重上层的问题上面。你可以得益于，使用上层的工具，而将打包部分的问题留给 webpack。
        
    * **webpack管理依赖生成依赖图**
        
        * webpack 管理文件之间的依赖，并将所有的资源(包括js和所有的非代码资源) 提供给应用程序使用
        
        * webpack 将所有的资源递归的构建为一个依赖图，并将所有的模块打包成为少量的几个 bundle 文件。
    
    * 重要的 四个 术语: 入口(entry),  出口(output),  loader,  插件(plugins)
    
    * entry (入口起点)
    
        entry 指示 webpack 应该使用那些模块，作为应用程序的入口文件。根据入口文件的引用，webpack 逐渐找出各个模块之间依赖的模块
        
        1. **模块的概念**
        
            一个文件中引入了另一个文件，被引入的文件成为一个模块 `在资源缓存三种代码分离部分有用到`
            
        2. entry 相关配置规则   `entry   string | array | object`
        
            * 一个数组的多个文件会被打包成为一个 chunk 文件
            
            * 单页应用对应多个入口起点，多页应用对应多个入口文件
            
            * entry 若单只传入一个字符串或者数组，chunk 会被命名为 main
            
    * output(出口) 
    
        **出口必须的两个参数 path, filename**
        
        示例：
        ```
        output: {
            path: path.resolve(__dirname, 'dist'),
            filename: 'my-first-webpack.bundle.js'
        }
        ```
        
    * **loader** （将任何引入的模块转化为JavaScript ）
    
        用于处理非JavaScript 的文件, webpack 本身只能理解 JavaScript。
        
        loader 能够将对应的文件转换为 能够使用 JavaScript 处理的文件
        
        loader 能够 import 导入任何类型的模块（例如 .css 文件），这是 webpack 特有的功能，其他打包程序或任务执行器的可能并不支持。
        
        **用法：loader 的使用（ 必须包含 test 和 use 属性 ）**

            test 属性:  识别出应该被对应的 loader 进行转换的那些文件。()
            
            use 属性: 转换这些文件，从而使其能够被添加到依赖图中（并且最终添加到 bundle 中）
            
    * plugins 插件
    
        插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能极其强大，可以用来处理各种各样的任务
        
        **用法**
        
            一般都需要使用 new 关键字生成一个新的实例
            
            多数插件都可以使用选项(option) 来自定义
            
    
2. 输入输出使用方法（ entry + output ）

    最佳实践: webpack 最好只配置一个入口文件
    
    * 单个入口的写法
        
        示例：
        ```
        entry: {
            main: './path/to/my/entry/file.js'      // 单个入口单个文件
            
            // 或者单个入口多个文件（数组的写法）
            
            main: ['./path/to/my/entry/file.js', './path/xxx.js']
        }
        ```
        
        数组的写法表示将多个文件打包到一个 chunk 文件(块文件，即 main.bundle.js),  即使使用数组的方法设置入口，也是只有一个入口文件
        
    * 多个入口的文件( 使用对象的方式 )
    
        示例
        ```
        entry: {
            app: './src/app.js',
            vendors: './src/vendors.js'
        }
        ```    
        
        多入口使用场景
        
        * 顾名思义， 多个入口即可以将其打包成为多个文件，用于多页面应用程序( 类比于单页应用)
        
            最佳实践： 多页应用也可以使用 CommonsChunkPlugin 为各个页面间创建共同的 bundle 块
            
        * 将资源缓存和代码分离
        
            最佳实践：将资源分三步分离出来，将公共的模块每次都进行缓存
        
            详情请学习 webpack 资源缓存和代码分离
            
    * 输出, 
    
        即使可以存在多个入口起点，但只指定一个输出配置.
        
        示例：
        ```
        output: {
            filename: 'bundle.js',
            path: '/home/proj/public/assets'
        }
        ```
        多个输入文件时，使用占位符只要保证输出的文件名称不重复即可以。
        
        示例：
        ```
        output: {
            filename: '[name].js',          // 根据入口名称输出相应的输出文件
            path: __dirname + '/dist'
        }
        ```
        
        * 高阶使用方法
        
            hash编译是否缓存的机制: 
            
            使用 hash 编译生成的文件名称后，更改文件内容后每次打包生成和引用的都是新的文件名称，保证无缓存文件，
            
            但是在打包时也可以指定未更改的文件不生成新的文件，而使用缓存的文件。
            
3. loader 功能生效在 `import` 或者加载文件 之前，对模块的源文件进行编译，将不同类型的文件，转为JavaScript 可以处理的文件

    示例：
    ```
    rules: [
        { test: /\.css$/, use: 'css-loader' },
        { test: /\.ts$/, use: 'ts-loader' }
    ]
    ```
    
    * loader 特性
        loader 支持链式调用，一组链式的loader 以相反的顺序依次执行加载。最后面的最先加载执行，直到 webpack 所期望的 JavaScript 或者 loader 完毕。
        
    * loader 接收查询参数。用于对 loader 传递配置。
    
    * loader 也能够使用 options 对象进行配置。
    
4. (Plugins)插件用于解决 loader 无法实现的事情

    webpack插件 是一个具有 apply 属性的 JavaScript 对象，在被 webpack compiler 对象调用时，在调用的整个生命周期内都可以使用。
    
    * 用法
    
        使用 new 创建实例，并且可以传入相应的参数
    
    * 配置示例：
    
        ```
        plugins: [
            new webpack.optimize.UglifyJsPlugin(),
            new HtmlWebpackPlugin({template: './src/index.html'})
        ]
        ```
    * 在nodeJs 中使用 webpack
    
        示例：
        ```
            const webpack = require('webpack'); //访问 webpack 运行时(runtime)
            const configuration = require('./webpack.config.js');
            
            let compiler = webpack(configuration);
            compiler.apply(new webpack.ProgressPlugin());
            
            compiler.run(function(err, stats) {
                // ...
            });
        ```
5. (Module)模块 和 依赖的图谱
    
    * **广义的概念**: 在模块化编程中，开发者将程序分解成离散功能块(discrete chunks of functionality)，并称之为模块。
    
    * **webpack 中的模块:** 任何可以被引入的资源都算作一个模块（js, css, image）
    
    * **依赖图**：
    
        * 具有依赖关系的资源都可以被 webpack 作为模块处理
        
        * webpack 从入口检查依赖关系，然后**递归的构建一个依赖图**, 将所有被引用的模块构建为 bundle 
        
    * 以下为 webpack 的示例
    
        * ES2015 import 语句
        
        * CommonJS require() 语句
        
        * AMD define 和 require 语句
        
        * css/sass/less 文件中的 @import 语句。
        
        * 样式(url(...))或 HTML 文件(<img src=...>)中的图片链接(image url)
    
6. (resolve)路径解析（ 解析路径是因为需要对引入的模块进行编译 ）

    `路径的解析规则：两个`
    
    * 若路径指向一个文件
    
        * 若该文件有文件名称, 则该文件被直接打包
        
        * 否则，将使用 [resolve.extensions] 选项作为文件扩展名来解析，此选项告诉解析器在解析中能够接受哪些扩展名（例如 .js, .jsx）。
        
    * 若路径指向一个文件夹
    
        * 如果文件夹中包含 package.json 文件，则按照顺序查找 resolve.mainFields 配置选项中指定的字段。并且 package.json 中的第一个这样的字段确定文件路径。
        
        * 如果 package.json 文件不存在或者 package.json 文件中的 main 字段没有返回一个有效路径，则按照顺序查找 resolve.mainFiles 配置选项中指定的文件名，看是否能在 import/require 目录下匹配到一个存在的文件名。
        
        * 文件扩展名通过 resolve.extensions 选项采用类似的方法进行解析。
        
    * 配置文件解析  ( 这个部分请配合上面的路径解析一块理解使用 )
    
        * resolve.mainFields        // type [array]
        
        此选项将决定在 package.json 中使用哪个字段导入模块。根据 webpack 配置中指定的 target 不同，默认值也会有所不同, 即 webpack 能够根据不同的环境切换 target
    
        ```
        // webpack 属性设置为 webworker, web 或者没有指定，默认 target 值为
        mainFields: ["browser", "module", "main"]
        
        // package.json
        {
            ...
            main: 'build/d3.Node.js',
            browser: 'build/d3.js',
            module: 'index',
            ...
        }
        
        ```
        
        * resolve.mainFiles     // type [array]
        
        ```
        webpack 解析目录时需要使用的文件名配置项，默认文件名称为 index
        mainFiles: ["index"]
        ```
        
7.  ( Hot Module Replacement )模块热替换

    * 保留在完全重新加载页面时丢失的应用程序状态。
    
    * 只更新变更内容，以节省宝贵的开发时间。
    
    * 调整样式更加快速 - 几乎相当于在浏览器调试器中更改样式。
    
    1. 热更新的运行步骤和情况
    
        应用程序 -> 编译器 -> 模块更新 -> HMR runtime
        
    > todo: 需要详解这四个过程执行的步骤
    