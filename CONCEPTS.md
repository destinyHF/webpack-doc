# webpack基本使用
## 一、entry
entry参数用于指定Webpack应该使用哪个模块开始构建其内部依赖关系图。

默认情况下，它的值为./src/index.js，该参数有三种配置方式：

#### 1、字符串形式

    module.exports = {
      entry:"./src/app.js"
    };
模块名：main（默认）
#### 2、数组形式

    module.exports = {
      entry:["./src/app.js","./src/admin.js"]
    }
模块名：main、chunk0
#### 3、对象形式

    moduel.exports = {
      entry:{
        app:"./src/app.js",
        admin:"./src/admin.js"
      }
    }
模块名：app、admin
## 二、output
output参数用于指定webpack如何输出文件到硬盘。
  
如果缺省配置，执行webpack打包命令默认会在根目录下的dist文件夹中生成输出文件，文件名为：main.js。

完整的output参数包含4个属性：
#### 1. filename 
指定输出文件名。对于单个entry，通常只需指定一个字符串。

    module.exports = {
      output:{
        filename:"bundle.js"
      }
    }
webpack也提供了一些“变量”来构建文件名：
* [id]：模块标识符
* [name]：模块名
* [hash:length]：模块标识符的哈希值
* [chunkhash:length]：块内容的哈希值
* [contenthash:length]：模块内容的哈希值

length的默认值是20，output的hashDigestLength属性可以改变这个默认值。

filename也可以是个函数：

    filename:chunkData=>{
      return `${chunkData.hash}.js`;
    }
chunkData是一个对象，包含了完整的输出描述。

注意，filename也是可以指定路径的，比如："./js/bundle.js"，相当于在dist/js/目录下生成改文件。
#### 2. path
指定所有输出文件的目标路径。这个路径必须是一个绝对路径，否则不会生效。

        module.exports = {
          output:{
            filename:"./js/bundle.js",
            path:path.resolve(__dirname,"dist","assets")
          }
        }
那么最终的输出路径就是path+filename：/dist/assets/js/bundle.js
#### 3. publicPath
指定html页面引用路径。为了直观一点，先安装一个插件：
    
    npm i html-webpack-plugin -D
配置webpack.config.js：

    const path = require("path");
    const HtmlWebpackPlugin = require("html-webpack-plugin");
    module.exports = {
      entry:["./src/app.js","./src/admin.js"],
      output:{
        filename:"bundle.js",
        path:path.resolve(__dirname,"dist","assets"),
        publicPath:"http://127.0.0.1/static"
      },
      plugins:[
        new HtmlWebpackPlugin({
          template:"./src/index.html",
          filename:"index.html"
        })
      ]
    };
html-webpack-plugin插件会将webpack输出的文件添加到目标html文件，所以最终输出文件本地路径是：

    /dist/assets/bundle.js
    /dist/assets/index.html
而index.html对bundle.js的引用路径不再是相对路径，而是：

    <script type="text/javascript" src="http://127.0.0.1/static/bundle.js"></script>
所以，通常publicPath参数是用来配置静态资源文件CDN的。
#### 4. libraryTarget



注意：虽然入口entry有多个，但出口只能有一个，所以如果entry配置了多个入口，那么webpack会按顺序将其打包成一个文件输出。
