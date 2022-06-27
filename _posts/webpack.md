---
title: webpack
date: 2021-11-19 10:11:21
tags: 前端
---

# 提取css成单独文件

```js
plugins: [
        //提取css单独打包
        new MiniCssExtractPlugin({
            filename: 'css/build.css'
        })
    ],
module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    // 'style-loader', 
                    //取代style-loader 作用:提取js中css成单独的文件
                    MiniCssExtractPlugin.loader,
                    //将css文件整合到js文件中
                    'css-loader'

                    //css兼容性处理:postcss-loader postcss-preset-env
                ]
            }
        ]
    },
```

# 压缩css

```js
const CssMinimizerWebpackPlugin = require('css-minimizer-webpack-plugin')

optimization: {
        minimizer: [
            new CssMinimizerWebpackPlugin()
        ]
    },
```

# babel-loader

将js文件中es6打包转为所有浏览器都能识别的es5

安装

```powershell
npm install -d babel-loader @babel/core @babel/preset-env
```

- babel-loader 在webpack里应用babel解析es6的桥梁
- @babel/core babel核心模块
- @babel/preset-env babel预设,一组babel插件的集合

初次之外 还需要regeneratorRuntime插件,这是webpack打包生成的全局辅助函数,由babel生成,用于兼容async/await语法

```powershell
#这个包中包含了regeneratorRuntime,运行时寻妖
npm install @babel/runtime -d

#这个插件会在需要regeneratorRuntime的地方自动require导包,编译时需要
npm install @babel/plugin-transform-runtime -d
```

最终在webpack.config.js中配置为

```js
		   {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['@babel/preset-env'],
                        plugins: [
                            [
                                '@babel/plugin-transform-runtime'
                            ]
                        ]
                    }
                }
            }
```

# 代码分离

用来获得更小的bundle,使bundle按需加载或者并行加载,常用的有三种方法

## 入口起点

使用entry配置手动的分离代码

缺点:如果是多个入口,那么每个入口中共享的文件会分别在每个包里重复去打包

```js
//入口文件
    entry: {
        index: './src/index.js',
        another: './src/another.js'	//这是另一个单独的js文件单独打包
    },

    output: {
        filename: '[name].bundle.js',  //打包后文件名称
        path: path.resolve(__dirname, './dist'),    //打包后文件存储地址
        clean: true, //是否自动删除之前旧的打包文件

        assetModuleFilename: 'image/[contenthash][ext]',    //定义资源模块的导出位置
    },
```

这样会生成两个打包文件, index.bundle.js 和 another.bundle.js

## 防止重复

使用Entry dependencies 或者 SplitChunksPlugin 去重和分离代码

### Entry dependencies

```js
//入口文件
    entry: {
        index:{
            import:'./src/index.js',
            dependOn:'shared'	//共享下面定义的重复模块,并生成一个shared.bundle.js
        },
        another:{
            import:'./src/index.js',
            dependOn:'shared'	//共享下面定义的重复模块,并生成一个shared.bundle.js
        },

        shared:'less'	//这样上面两个打包时如果都用到了less模块就会共享
    },
```

比如两个js文件均引入了less模块:import less from 'less',如果普通的打包方式两个打包后的文件分别大小,分别为1.32M 和 1.2M.如下

![image-20211119155403269](D:\devApp\myblog\source\_posts\webpack\image-20211119155403269.png)

如果改为这种防止重复的方法则共有三个打包文件,如下

![image-20211119155530856](D:\devApp\myblog\source\_posts\webpack\image-20211119155530856.png)

其中shared.bundle.js是打包共享模块的打包文件,另外两个明显缩小

### SplitChunksPlugin

```js
entry: {
    index: './src/index.js',
    another: './src/another.js',
},
optimization: {
    splitChunks: {
        chunks: 'all'
    }
},
```

这样会自动抽离公共模块生成额外两个打包文件

## 动态导入

通过模块的内联函数调用来分离代码.使用了async提供的import的函数

新建一个async-module.js文件,如下

```js
//这样写的目的是为了让import帮助我们去抽离出一个单独的lodash文件
function getComponent() {
    //import 返回的是一个promise
    return import('lodash').then(({ default: _ }) => {
        const element = document.createElement('div')
        element.innerHTML = _join(["hello", "webpack"], ' ')
        return element
    })
}

getComponent().then((element) => {
    document.appendChild(element)
})

```

webpack.config.js如下,仅仅使用最简单的方式打包

```js
//入口文件
    entry: {
        index: './src/index.js',
    },
```

在index.js中引入async-module.js

这样打包后会单独分离出一个lodash的打包文件

![image-20211122094729606](D:\devApp\myblog\source\_posts\webpack\image-20211122094729606.png)

动态导入和前面的静态方法可以一起使用

## 动态导入的应用

### 懒加载

又称为按需加载.懒加载是动态导入代码分离方法的一种应用

因为有一些模块应用的很少,或者根本不会被引用.所以这些时候可以在需要的是在才对这些模块进行加载,从而得到优化

新建一个math.js文件如下

```js
export function add(x, y) {
    return x + y
}

export function sub(x, y) {
    return x - y
}
```

在index.js中新增一个按钮,用来加载math.js并调用其中的方法

```js

const button = document.createElement('button')
button.textContent = "点击执行10+10"
button.addEventListener('click', () => {
    import('./math').then(({ add }) => {
        console.log(add(10, 10));

    })
})

//这样可以自定义生成的打包文件的名称,这样math文件打包出来的名字为math.bundle.js
//button.addEventListener('click', () => {
//    import(/*webpackChunkName:'math'*/'./math').then(({ add }) => {
//        console.log(add(10, 10));
//    })
//})
```

这样打包后会生成一个新的单独的math的打包文件,其中包含了math.js中定义的两个方法,从而在需要这个包的的时候才会去加载

![image-20211122104318242](D:\devApp\myblog\source\_posts\webpack\image-20211122104318242.png)

这时在浏览器中初启动时并不会请求加载math打包的相关文件

![image-20211122104521719](D:\devApp\myblog\source\_posts\webpack\image-20211122104521719.png)

当点击按钮后

![image-20211122104640941](D:\devApp\myblog\source\_posts\webpack\image-20211122104640941.png)

这时才会去请求加载math的打包文件

### 预获取和预加载

预获取

在下一个页面即将用到的资源可以在父页面中进行预加载,他会在父页面所有其他资源加载完毕后,在浏览器网络空闲时进行下载.

```js
//这样可以自定义生成的打包文件的名称,这样math文件打包出来的名字为math.bundle.js
button.addEventListener('click', () => {
    import(/*webpackChunkName:'math',webpackPrefetch:true*/'./math').then(({ add }) => {
        console.log(add(10, 10));
    })
})
```

预加载

```
//这样可以自定义生成的打包文件的名称,这样math文件打包出来的名字为math.bundle.js
button.addEventListener('click', () => {
    import(/*webpackChunkName:'math',webpackPreload:true*/'./math').then(({ add }) => {
        console.log(add(10, 10));
    })
})
```

在资源上添加预先加载的注释，你指明该模块需要立即被使用。**异步chunk**会和**父级chunk**并行加载。如果**父级chunk**先下载好，页面就已可显示了，同时等待**异步chunk**的下载。这能大幅提升性能。

# 拆分配置文件及合并

新建config文件夹

新建webpack.config.common.js这里面写开发环境和生产环境公共的配置

不同的是:

- output中的filename:在开发环境中由于不需要浏览器的缓存功能,所以不需要每次更新打包文件都更改打包名称,所以在开发环境中固定打包名称,在生产环境中加入hash使每次更新打包文件都声称新的不同的名称,触发浏览器更新缓存
- output中的publicPath:在开发环境中只用于测试,无需设置公共路径.但是在生产环境中需要设置公共路径localhost:8080/
- devtool:在开发环境中需要堆代码进行调试,查找报错位置,需要devtool直接定位到错误位置
- devServer:在开发环境中要自动生成html文件并在修改代码时是页面自动更新,即npx webpack-dev-server
- mode:模式

```js
/**
 * loader: 1下载 2使用(配置loader)
 * plugins: 1下载 2引入 3使用
 */

const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const { node } = require('webpack')

module.exports = {
    //入口文件
    entry: {
        index: './src/index.js',
        another: './src/another.js',
    },

    output: {
        //中间的contenthash是因为浏览器会有缓存功能,而每次更新打包后名字不变的话会浏览器会认为没有更新
        //从而浏览及会继续使用缓存中的文件,不会进行更新
        //使用contenthash会让每次打包的文件根据内容生成hash从而改变文件名
        path: path.resolve(__dirname, '../dist'),    //打包后文件存储地址
        clean: true, //是否自动删除之前旧的打包文件
        assetModuleFilename: 'image/[contenthash][ext]',    //定义资源模块的导出位置
    },

    //插件的配置
    plugins: [
        //自动打包生成html文件,并
        new HtmlWebpackPlugin({
            template: './index.html',   //以此文件为模板从而引入入口文件打包后所有资源
            filename: 'app.html',
            inject: 'body'  //设置script在哪个位置插入
        }),
        new MiniCssExtractPlugin()
    ],

    //loader的配置
    module: {
        rules: [
            //处理图片资源
            {
                test: /\.(png|jpg|gif)$/,   //匹配哪些文件(此处是处理后缀名为png的文件)
                //这个type共有三种可选
                //1.asset/resource是将图片地址转为打包的图片在打包后目录中的url
                //2.asset/inline是将图片地址转为base64处理的地址
                //3.asset/resource用于导出资源的源代码,一般用于text
                type: 'asset/resource',
                generator: {
                    filename: 'image/[contenthash].[ext]'   //定义打包后的图片路径,优先级高于上面output中assetModuleFilename
                }
            },
            //处理less样式资源
            {
                test: /\.less$/,  //打包样式资源
                //要使用多个loader做处理用use,使用单个loader就能处理的话用loader就行
                use: [
                    //use数组中loader执行顺序:从右到左,从下到上 依次执行
                    //创建style标签,将js中的样式资源插入进去,添加到header中生效
                    'style-loader',
                    //将css文件变成commonjs模块加载js中,里面内容是样式字符串
                    'css-loader',
                    //将less文件编译成css文件
                    'less-loader'
                ]
            },
            {
                test: /\.css$/,  //打包样式资源
                use: [
                    //use数组中loader执行顺序:从右到左,从下到上 依次执行
                    //创建style标签,将js中的样式资源插入进去,添加到header中生效
                    'style-loader',
                    //将css文件打包变成commonjs模块加载js中,里面内容是样式字符串
                    'css-loader'
                ]
            },
            {
                test: /\.png$/,   //匹配哪些文件(此处是处理后缀名为png的文件)
                type: 'asset/resource',
                generator: {
                    filename: 'image/[contenthash][ext]'   //定义打包后的图片路径,优先级高于上面output中assetModuleFilename
                }
            },
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['@babel/preset-env'],
                        plugins: [
                            [
                                '@babel/plugin-transform-runtime'
                            ]
                        ]
                    }
                }
            }

        ]
    },

    optimization: {
        splitChunks: {
            // chunks: 'all'
            //缓存组,上面输出中使每个文件打包后名称都进行更新
            //但是想某些单独抽离出去的模块并不会被修改,第三方库,比如lodash
            //这种情况下希望浏览器只加载一次,以后都用缓存的中这些模块
            //在这里配置缓存组
            cacheGroups: {
                vendors: {
                    test: '/[\\/]node_modules[\\/]/',
                    name: 'vendors',
                    chunks: 'all'
                }
            }
        }
    },

}
```

新建webpack.config.dev.js 这里配置开发环境中需要的特有配置

```js
module.exports = {

    output: {
        filename: 'js/[name].js',  //打包后文件名称
    },

    devtool: 'inline-source-map',   //开发时报错直接定位到报错的位置

    //自动化 自动编译 自动打开浏览器 自动刷洗浏览器
    //特点 只会在内存中打包,不回有任何输出
    //启动指令:npx webpack-dev-server
    devServer: {
        //启动gzip压缩
        compress: true,
        //端口号
        port: 8080,
        static: './dist',
        open: true
    },
    mode: 'development',
}
```

新建webpack.config.pro.js 这里配置生产环境中需要的特有配置

```js
module.exports = {

    output: {
        //中间的contenthash是因为浏览器会有缓存功能,而每次更新打包后名字不变的话会浏览器会认为没有更新
        //从而浏览及会继续使用缓存中的文件,不会进行更新
        //使用contenthash会让每次打包的文件根据内容生成hash从而改变文件名
        filename: 'js/[name].[contenthash].js',  //打包后文件名称
        publicPath: 'localhost:8080/',   //公共路径
    },
    mode: 'production',
}
```

下载webpack-merge包用于合并配置

新建webpack.config.js文件,在其中对命令进行判断,从而决定怎么合并

```js
const { merge } = require('webpack-merge')
const commonConfig = require('./webpack.config.common')
const productionConfig = require('./webpack.config.pro')
const developmentConfig = require('./webpack.config.dev')

module.exports = (env) => {
    switch (true) {
        case env.development:
            return merge(commonConfig, developmentConfig)
        case env.production:
            return merge(commonConfig, productionConfig)
        default:
            return new Error('没有匹配到env环境')
    }
}
```

在package.json中

```json
"scripts": {
    "start": "npx webpack server -c ./config/webpack.config.js --env development",
    "build": "npx webpack -c ./config/webpack.config.js --env production"
  },
```

# source-map

webpack内置source-map,由于浏览器加载的是打包好的bundle.js文件,所以在报错定位时查看到的也是bundle文件.上面也已经用到了

```js
 devtool: 'inline-source-map',   //开发时报错直接定位到报错的位置
```

source-map有好多种可选值,当不设置的时候默认为eval

- eval	将每一个module封装到eval里包裹 起来执行,并且会在末尾加注释//@sourceURL
- source-map 生成source-map 的打包文件

# devServer

```js
	//自动化 自动编译 自动打开浏览器 自动刷洗浏览器
    //特点 只会在内存中打包,不回有任何输出
    //启动指令:npx webpack-dev-server
    devServer: {
        static: path.resolve(__dirname, '../dist'),
        //启动gzip压缩(使打包文件从服务器传到浏览器是经过压缩的,提高速度)
        compress: true,
        //端口号
        port: 8080,
        open: true,

        //添加响应头
        headers: {
            'X-Access-Token': 'abc123'
        }
        //配置代理
        proxy: {
            '/api': 'http://localhost:9000'
        },
        //当我们的应用是个SPA(单页面应用),当路由到/some时,此时刷新页面可能会报错
        historyApiFallback:true
    }
```

# 优化开发环境

## 优化打包构建速度

### oneOf

每个文件都会被rules中的所有loader匹配处理,使用oneOf则在oneOf中的loader只会匹配一个,提高构建速度.但是注意不能有两个配置处理同一类型的文件.

```js
//loader的配置
        module: {
            rules: [
                {
                    oneOf: [
                        //处理图片资源
                        {
                            test: /\.(png|jpg|gif)$/,   //匹配哪些文件(此处是处理后缀名为png的文件)
                            //这个type共有三种可选
                            //1.asset/resource是将图片地址转为打包的图片在打包后目录中的url
                            //2.asset/inline是将图片地址转为base64处理的地址
                            //3.asset/resource用于导出资源的源代码,一般用于text
                            type: 'asset/resource',
                            generator: {
                                filename: 'image/[contenthash].[ext]'   //定义打包后的图片路径,优先级高于上面output中assetModuleFilename
                            }
                        },
                        //处理less样式资源
                        {
                            test: /\.less$/,  //打包样式资源
                            //要使用多个loader做处理用use,使用单个loader就能处理的话用loader就行
                            use: [
                                //use数组中loader执行顺序:从右到左,从下到上 依次执行
                                //创建style标签,将js中的样式资源插入进去,添加到header中生效
                                'style-loader',
                                //将css文件变成commonjs模块加载js中,里面内容是样式字符串
                                'css-loader',
                                //将less文件编译成css文件
                                'less-loader'
                            ]
                        },
                        {
                            test: /\.css$/,  //打包样式资源
                            use: [
                                //use数组中loader执行顺序:从右到左,从下到上 依次执行
                                //创建style标签,将js中的样式资源插入进去,添加到header中生效
                                // 'style-loader',
                                //取代style-loader 作用:提取js中css成单独的文件
                                MiniCssExtractPlugin.loader,
                                //将css文件打包变成commonjs模块加载js中,里面内容是样式字符串
                                'css-loader'
                            ],
                        },
                        {
                            test: /\.png$/,   //匹配哪些文件(此处是处理后缀名为png的文件)
                            type: 'asset/resource',
                            generator: {
                                filename: 'image/[contenthash][ext]'   //定义打包后的图片路径,优先级高于上面output中assetModuleFilename
                            }
                        },
                        {
                            test: /\.js$/,
                            exclude: /node_modules/,
                            use: {
                                loader: 'babel-loader',
                                options: {
                                    presets: ['@babel/preset-env'],
                                    plugins: [
                                        [
                                            '@babel/plugin-transform-runtime'
                                        ]
                                    ]
                                }
                            }
                        }
                    ]
                }

            ]
        },
```

# tree shaking

去除无用代码,即使引用了包但是没有使用也会自动去除.但是不能百分百的tree shaking

前提:必须使用ES6模块化,开启production环境

```js
mode:'production',
optimization: {
        usedExports: true
    },
```

## sideEffects

有些引入的包虽然没使用但是有用,而有些不是,使用sideEffects可以告诉webpack哪些包是必要的不能tree shaking.

在package.json中设置sideEffects,共有三个可选值

- true 我的所有代码都是有用的,不能随意去除
- false 我所有的包都是没有用的,那么一些引入的包,但是代码中并没有使用的话就会被去除(比如引入的css)
- [] 可以使用一个数组定义哪些有用,不能删除(如 sideEffects:["*.css"] )

# 模块热替换(HMR)与热加载

## 模块热替换

模块热替换会在应用程序运行过程中,替换,添加或删除模块,而**无需重新加载整个页面**.开启方式只需在devServer下配置hot:true即可.

比如当更改css文件的某一个背景颜色时,不开启热替换的话需要刷新页面才能显示新的页面.但是如果开启了热替换的话则无需刷新页面直接自动更新.

不过js文件即使开启了热替换也不能进行自动替换,仍会刷新界面才能展示新的效果.这时需要手动进行判断并配置

比如input.js如下

```js
document.querySelector('#box').innerHTML = '<input type="text" value="input1"/>'
```

在入口文件中引入input.js,进行如下配置

```js
if (module.hot) {
    module.hot.accept('./input.js', () => {

    })
}
```

这样如果在input.js中改变value的值的话,就能进行自动热替换了.vue中已经自动帮助我们做好了

## 热加载

文件更新时自动刷新服务和页面.新版的webpack-dev-server默认已经开启了热加载的功能,他对应的参数是devServer.liveReload,默认为true.注意:如果想要关掉他,要将liveReload设置为false的同时,也要关闭hot.

# 模块解析(resolve)

在resolve中设置alias可以设置别名.extensions可以在当前路径中存在文件名相同,扩展名不同的文件,按照extensions配置的顺序优先解析

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')
const path = require('path')
module.exports = {
    mode: 'development',
    entry: './src/app.js',
    
    resolve: {
        //设置路径别名
        alias: {
            '@': path.resolve(__dirname, './src')
        },
        //如果在当前路径中存在文件名相同,扩展名不同的文件
        //按照下面配置的顺序优先解析
        extensions:['.json','.js','.vue']
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './index.html'
        })
    ],
    devServer: {
        hot: true
    }
}
```

# externals

`externals` 配置选项提供了「从输出的 bundle 中排除依赖」的方法。

# 多页面

## entry配置

将部分不相关的js文件打包成一个文件

```js
 entry: {
        main: ['./src/js/app.js', './src/js/app2.js'],
        another: './src/js/another.js'
    },
```

这样'./src/js/app.js', './src/js/app2.js'会打包到一个main.js中,而./src/js/another.js会在另一个中.

## HtmlWebpackPlugin

HtmlWebpackPlugin中可以配置当前页面可以载入哪些chunk.这样打包好的html中只引入了main.js打包文件

```js
plugins: [
        new HtmlWebpackPlugin({
            template: './index.html',
            inject:'body',
            //根据上面入口中的配置选择需要载入的文件
            chunks:['main']
        })
    ],
```

## 多页面环境搭建

主要是配置两个htmlwebpackplugin

```js
plugins: [
        new HtmlWebpackPlugin({
            template: './index.html',
            inject: 'body',
            filename: 'chanel1/index.html',
            //根据上面入口中的配置选择需要载入的文件
            chunks: ['main']
        }),
        new HtmlWebpackPlugin({
            template: './index2.html',
            inject: 'body',
            filename: 'chanel2/index2.html',
            //根据上面入口中的配置选择需要载入的文件
            chunks: ['another']
        }),
    ],
```

# 渐进式网络应用程序(PWA)

共两步

## 添加Workbox

npm install workbox-webpack-plugin -d	安装workbox

```js
const WorkboxPlugin = require('workbox-webpack-plugin')

plugins: [
        new HtmlWebpackPlugin({
            template: './index.html',
            inject: 'body',
            filename: 'index.html',
        }),
        new WorkboxPlugin.GenerateSW({
            clientsClaim: true,
            skipWaiting: true
        })
    ],
```

## 在浏览器中注册Service-Worker

在入口文件中

```js
if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
        navigator.serviceWorker.register('/service-worker.js')
            .then(registration => {
                console.log('注册成功', registration);
            })
            .catch(registrationError => {
                console.log('注册失败', registrationError);

            })
    })
}
```

经过这两步之后,就能实现即使服务器挂掉,静态页面仍能显示.

# dll



