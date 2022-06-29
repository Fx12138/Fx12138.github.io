# 核心概念

四个核心概念:入口(entry) 	输出(output) 	loader	插件(plugins) 

## loader

*loader* 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。loader 可以将所有类型的文件转换为 webpack 能够处理的有效[模块](https://www.webpackjs.com/concepts/modules)，然后你就可以利用 webpack 的打包能力，对它们进行处理。

本质上，webpack loader 将所有类型的文件，转换为应用程序的依赖图（和最终的 bundle）可以直接引用的模块。

在更高层面，在 webpack 的配置中 **loader** 有两个目标：

1. `test` 属性，用于标识出应该被对应的 loader 进行转换的某个或某些文件。
2. `use` 属性，表示进行转换时，应该使用哪个 loader。

## plugins

loader 被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。[插件接口](https://www.webpackjs.com/api/plugins)功能极其强大，可以用来处理各种各样的任务。

想要使用一个插件，你只需要 `require()` 它，然后把它添加到 `plugins` 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 `new` 操作符来创建它的一个实例。

# 资源模块

- asset/resource 发送一个单独的文件并导出 URL。resource类型打包后，会在dist下面生成对应的静态资源文件。从而需要一次请求得到图片
- asset/inline 导出一个资源的 data URI。比如svg格式导出为base64的格式。inline类型打包后，dist下不会生成对应的静态资源文件。导出的base64直接放在src中所以不用发请求了,但是图片越大,生成的base64体积会相对于原图片变得更大,因此适用于图片体积较小的情况
- asset/source 导出资源的源代码。比如实现的功能为在网页上渲染txt文件中的内容。source类型打包后，dist下不会生成对应的静态资源文件。
- asset 在导出一个 data URI 和发送一个单独的文件之间自动选择。在resource和inline中自动选择；默认选择方式如果文件大于8k，选择resource的类型；如果文件小于8k，选择inline的类型。也可以通过parser下的dataUrlCondition来修改这个默认规则

## Resource资源

```js
  module: {
    rules: [
      {
        test: /\.png$/, //匹配的文件名
        type: 'asset/resource'  //使用的模式
      }
    ]
  },
```

可以自定义打包后的位置和文件名,在output中进行配置

```js
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist'),
    clean: true,  //用于自动清理上次打包的文件
    assetModuleFilename: 'images/[contenthash][ext]'	//自定义资源模块打包的位置和文件名
  },
```

也可以在loader里为一个文件单独设置打包后的位置和文件名,使用generator,当assetModuleFilename和generator同时存在时,generator优先级高

```js
  module: {
    rules: [
      {
        test: /\.png$/, //匹配的文件名
        type: 'asset/resource',  //使用的模式
        generator: {
          filename: 'images/test.png' //自定义资源模块打包的位置和文件名
        }
      }
    ]
  },
```

# loader

## 处理css资源



需要css-loader 和 style-loader 两个loader,其中css-loader是用来打包css文件,而style-loader用于将效果在页面中展示

```js
{
  test: /\.css$/,
  use: ['style-loader', 'css-loader']
}
```

less文件需要在这两个loader后面加一个less-loader

## babel

主要用于将ES6语法编写的代码转换为向后兼容的js语法,以便能够运行在当前和旧版本的浏览器或其他环境中.新建babel.config.js文件

主要配置是预设presets,简单理解就是一组babel插件扩展babel的功能

```powershell
npm install babel-loader babel-core babel-preset-env -d
```

可以直接在webpack.config.js中进行配置

```js
      {
        test: /\.js$/,
        exclude: /(node_modules|bower_components)/, //排除node_modules中的文件不处理
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
```

也可以新建babel.config.js文件进行配置,方便以后的修改

```js
module.exports = {
  //智能预设 能够编译ES6的语法
  presets: ['@babel/preset-env']
}
```

# 开发环境

在根目录下新建config文件夹,在config文件夹中创建webpack.dev.js和webpack.prod.js分别为开发环境和生产环境的配置

在package.json中配置启动命令

```json
 "scripts": {
    "start": "npm run dev",
    "dev": "webpack serve --config ./config/webpack.dev.js",
    "build": "webpack --config ./config/webpack.prod.js"
  },
```

## HtmlWebpackPlugin

作用:生成index.html自动引入打包好的资源

```js
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, '../public/index.html'),  //根据哪个模板
    })
  ],
```

## source map

作用:在开发环境中,代码报错时需要准确的定位到报错位置,但是默认生成的bundle中已经将代码进行压缩,并不能正确的进行定位,使用source map可以实现

```js
  devtool: 'inline-source-map',
```

## webpack-dev-server

即使有了上面的watch可以在保存时进行自动打包,但是用户每次还需要刷新一下浏览器才能显示更新后的内容,使用webpack-dev-server可以解决这个问题,它具有live reloading即实时加载页面的功能

其实他没有真正的输出任何物理文件,而是把输出的bundle文件放到了内存里

```js
  devServer: {
    host: 'localhost',
    port: '3000',
    open: true
  },
```

```powershell
npx webpack serve
```

# 生产环境

在生产环境中不需要devServer

## HtmlWebpackPlugin

作用:生成index.html自动引入打包好的资源

```js
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, '../public/index.html'),  //根据哪个模板
    })
  ],
```

## 抽离和压缩css

使用mini-css-extract-plugin将css抽离成单独的文件并使用link标签引入.因为要引入并不需要用style-loader将style标签写在head中,所以需要将style-loader换成MiniCssExtractPlugin.loader

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module: {
    rules: [
      {
        test: /(\.css|less)$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'less-loader']
      }
    ]
  },

plugins: [
    new MiniCssExtractPlugin({
      filename: 'style/[contenthash].css'	//自定义打包后文件的位置和名称
    })
  ],
```

这个时候已经能够成功抽离css文件,但是打包后的css文件没有进行压缩,这时候要用到css-minimizer-webpack-plugin,这个plugin比较特殊,他不在plugin中进行配置,而是在optimization优化中进行配置,并且需要在生产模式下

```js
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin')

mode: 'production',

optimization: {
    minimizer: [
      new CssMinimizerPlugin()
    ]
  }
```

## 样式兼容性处理

```powershell
npm install postcss-loader postcss postcss-preset-env -d
```

然后进行配置,注意这个loader的配置要写在css-loader的后面并且在less-loader的前面

```js
{
        test: /(\.less)$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader', {
          loader: "postcss-loader",
          options: {
            postcssOptions: {
              plugins: [
                [
                  "postcss-preset-env",
                  {
                    // Options
                  },
                ],
              ],
            },
          },
        },, 'less-loader']
      },
```

需要在package.json中配置浏览器要兼容的版本,比如

```json
  "browserslist": [
    "ie >= 8"
  ]
```

# 高级

高级配置就是进行webpack优化,让代码再编译/运行时性能更好

## 提升打包构建速度

### HMR

HMR全称HotModuleReplacement 热模块替换,提高开发环境中项目的打包速度

开发时修改了其中一个模块的代码,但是webpack默认会将所有模块全部重新打包编译,速度很慢.所以需要做到当我们修改某个模块的代码,就只对这个模块代码进行重新打包编译,其他模块不变提高打包速度

在webpack5中样式默认开启热模块替换,但是js不行,默认情况下修改js代码依然会重新对所有模块进行编译

在index.js中对需要进行热模块替换的js引入进行配置

```js
if(module.hot){
  //判断是否支持热模块替换功能
  module.accept("./helloWorld")
}
```

这样配置后,当helloWorld.js文件发生变化后,只会对这个文件进行单独的重新编译,其他文件不会

开发项目时使用vue-loader或react-hot-loader会自动进行热模块替换,无需手动配置

### OneOf

开发模式和生产模式都可以

在编译打包时,遇到非js文件会在loader中由上到下依次寻找能够处理这个文件的loader,一直到结束.使用OneOf可以实现在寻找的过程中一旦命中,就不再向后寻找,直接使用

使用方法:只需在rules和匹配规则中间加一层oneOf包裹

```js
module: {
    rules: [
      {
        oneOf: [
          {
            test: /\.png$/, //匹配的文件名
            type: 'asset/resource',  //使用的模式
            generator: {
              filename: 'images/test.png' //自定义资源模块打包的位置和文件名
            }
          },
          {
            test: /(\.css)$/,
            use: ["style-loader", 'css-loader']
          },
          {
            test: /(\.css|less)$/,
            use: ["style-loader", 'css-loader', 'less-loader']
          },
          {
            test: /\.js$/,
            exclude: /(node_modules|bower_components)/, //排除node_modules中的文件不处理
            loader: 'babel-loader',
            // options: {
            //   presets: ['@babel/preset-env']
            // }
          }
        ]
      }
    ]
  },
```

### include/exclude

我们使用的第三方库或插件,比如vue-router echarts 这些文件都下载到了node_modules中了,而这些文件不需要再次进行编译,所以需要排除这里面的文件

主要是针对js文件,如babel和eslint

```js
{
  test: /\.js$/,
  exclude: /(node_modules|bower_components)/, //排除node_modules中的文件不处理
  include:path.resolve(__diename,"../src")//只处理src目录下的文件
  loader: 'babel-loader',
  // options: {
   //   presets: ['@babel/preset-env']
  // }
}
```

### chche

开发模式和生产模式,每次打包时都要经过Eslint检查和Babel编译,速度比较慢

可以缓存之前的Eslint检查和babel编译结果,这样第二次打包时只会打包后来修改的,之前的会使用缓存,从而提高打包速度

```js
{
  test: /\.js$/,
  exclude: /(node_modules|bower_components)/, //排除node_modules中的文件不处理
  loader: 'babel-loader',
  options: {
    // presets: ['@babel/preset-env'],
    cacheDirectory:true,  //开启babel缓存
    cacheCompression:false  //关闭缓存文件压缩
  }
}
```

### 多进程打包

想要提升打包速度,主要是提升js打包速度.因为js打包主要是采用Eslint babel terser(webpack内置的对js进行压缩)这些工具进行处理打包,所以我们要提升他们的运行速度,我们可以开启多进程同时处理js文件.**注意**请在特别耗时的操作中进行,因为每个进程启动就有大约600ms左右的时间

使用时需要先判断cpu核数,因为启动进程的最大数就是cpu的核数

```js
const os = require('os')
//获取cpu核数
const threads = os.cpus().length;
```

babel开启多进程打包

```powershell
npm install thread-loader -d
```

```js
{
  test: /\.js$/,
  exclude: /(node_modules|bower_components)/, //排除node_modules中的文件不处理
  use: [
    {
      loader: 'thread-loader', //开启多进程
      options: {
        works: threads //进程的数量
      }
    },
    {
      loader: 'babel-loader',
      options: {
        // presets: ['@babel/preset-env'],
        cacheDirectory: true,  //开启babel缓存
        cacheCompression: false  //关闭缓存文件压缩
      }
    }
  ]
}
```

terser开启多进程打包

```js
const TerserWebpackPlugin = require('terser-webpack-plugin')
```

```js
  optimization: {
    //压缩的操作
    minimizer: [
      //压缩css文件
      new CssMinimizerPlugin(),
      //压缩js
      new TerserWebpackPlugin({
        parallel: threads   //开启terser多进程打包和进程数量
      })
    ]
  },
```

## 减少代码体积

### Tree Shaking

开发时定义了一些工具函数库,或者引用第三方工具函数库或组件库.如果没有特殊处理的话打包时就会引入整个库,但是实际上我们可能只用上极小部分的功能

TreeShaking它依赖ESModule会自动移除没有使用的代码,webpack已经默认开启,无需做其他配置

### 减少Babel生成文件体积

开发和生产中,babel为编译的每个文件都插入了辅助代码,使代码体积过大

@babel/plugin-transform-runtime 仅用了babel自动对每个文件的runtime注入,而是引入@babel/plugin-transform-runtime并且是所有辅助代码从这里引用

```powershell
npm install @babel/plugin-transform-runtime -d
```

```js
{
  loader: 'babel-loader',
  options: {
    // presets: ['@babel/preset-env'],
    cacheDirectory: true,  //开启babel缓存
    cacheCompression: false,  //关闭缓存文件压缩
    plugins:["@babel/plugin-transform-runtime"] //减少体积
  }
}
```

### 压缩图片

对图片进行压缩,如果项目中都是在线链接那么就不需要了,只有本地项目静态图片才需要进行压缩.使用image-minimizer-webpack-plugin

```powershell
npm install image-minimizer-webpack-plugin imagemin -d
```

压缩分为无损压缩和有损压缩两种模式

无损压缩

```js
npm install imagemin-gifsicle imagemin-jpegtran imagemin-optipng imagemin-svgo -dev
```

有损压缩

```js
npm install imagemin-gifsicle imagemin-mozjpeg imagemin-pngquant imagemin-svgo -dev
```

## 优化代码运行性能

### code split

打包代码时会将所有js文件打包到一个文件中,体积太大了,如果只要渲染首页,就应该只加载首页的js文件,其他文件不应该加载

代码分割主要做了两件事

- 分割文件:将打包生成的文件进分割,生成多个js文件
- 按需加载:需要哪个文件就加载哪个文件

#### 多入口

```js
  entry: {
    main: './src/index.js',
    other: './src/other.js'
  },

  output: {
    filename: '[name].js',  //webpack的命名方式,以自身的文件名命名
    path: path.resolve(__dirname, '../dist'),
    clean: true,  //用于自动清理上次打包的文件
    assetModuleFilename: 'images/[contenthash][ext]'  //自定义资源模块打包的位置和文件名
  },
```

#### 多入口提取公共模块

如果入口文件都引用了都同一份代码,则webpack会在每个生成的打包文件中引用一次,但是想要的效果是公共的引用单独打包成一个文件,然后他们进行复用,这样只打包了一次

比如两个js文件均引入了less模块:import less from ‘less’,如果普通的打包方式两个打包后的文件分别大小,分别为1.32M 和 1.2M

```js
optimization: {
    splitChunks: {
        chunks: 'all'
    }
},
```

代码分割后会生成第三个文件,内容是公共模块的单独打包

在单文件入口中一样可以直接使用

#### 多入口按需加载(动态导入)

比如刚开始不需要加载某个js文件,就让它最开始不要加载,当点击某个按钮或者进行了某些操作的时候再引入这个js文件

import动态导入会将动态导入的文件代码分割(拆分成单独模块),在需要使用的时候自动加载

 比如在一个click操作中,这样可以自定义生成的打包文件的名称,这样math文件打包出来的名字为math.js

```js
document.getElementById("btn").onclick = function(){
	import(/*webpackChunkName:'math'*/"./math")
		.then(res=>{
			console.log('模块加载成功',res)
		})
		.catch(err=>{
			console.log("模块加载失败",err)
		})
}
```

但是这样打包出来单独的文件是随机命名的,并没有使用定义好的math名称,需要在ouput中进行配置chunkFilename

```js
output: {
    filename: 'bundle.js',  //webpack的命名方式,以自身的文件名命名
    chunkFilename: '[name].chunk.js',
    path: path.resolve(__dirname, '../dist'), //打包输出的其他文件命名,比如动态加载的文件
    clean: true,  //用于自动清理上次打包的文件
    assetModuleFilename: 'images/[hash:10][ext]'  //自定义资源模块打包的位置和文件名
  },
```

### 预获取和预加载

共同点:只加载,不执行.缓存起来,当用的时候进行调用

预获取(兼容性较差)

在下一个页面即将用到的资源可以在父页面中进行预加载,他会在父页面所有其他资源加载完毕后,在浏览器网络空闲时进行下载.

```js
button.addEventListener('click', () => { import(/*webpackChunkName:'math',webpackPrefetch:true*/'./math').then(({ add }) => {
        console.log(add(10, 10));
    })
})
```



预加载(兼容性还行)

在资源上添加预先加载的注释，你指明该模块需要立即被使用。**异步chunk**会和**父级chunk**并行加载。如果**父级chunk**先下载好，页面就已可显示了，同时等待**异步chunk**的下载。这能大幅提升性能。

```js
button.addEventListener('click', () => {   			import(/*webpackChunkName:'math',webpackPreload:true*/'./math').then(({ add }) => {
        console.log(add(10, 10));
    })
})
```

### 渐进式网络应用程序(PWA)

开发的web项目,一旦处于网络离线状况,就没法访问了,我们希望给项目提供离线体验.内部通过service workers实现

```powershell
npm install workbox-webpack-plugin -d
```

```
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

这时还不能生效,需要在js代码中注册

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

