# Apite 简介
apite 是 api + lite 的简写，读：[əˈpaɪt]，是一个基于nodejs的轻量级api生成工具。

主要功能点有：
- 定义api规范，自动生成api文档
- 支持数据模拟服务，可在线进行联调接口
- 支持代理和mock无缝过渡
- 支持react、vue等基于webpack的脚手架以及vite
- 支持cli命令行运行

## 快速开始

- **全局命令行使用**
  ```bash
  # 全局安装，只用安装一次
  npm i -g apite

  # 安装完成后，如果不想生成过多的文件，此时可以直接执行 apite 命令，不需要 apite init, 此操作不会初始化api的示例文件。
  # 初始化项目
  apite init

  # 启动命令 or 直接运行 apite
  npm run apite 
  ```
  浏览器打开 [localhost:3000](http://localhost:3000/) 查看api文档

- **独立项目使用**
  ```bash
  # 安装依赖
  npm i -S apite

  # package.json 添加命令
  {
    "scripts": {
      "apite": "apite --dir=api",
    }
  }

  # 启动命令 or 直接运行 apite （全局安装apite）
  npm run apite
  ```
  用浏览器打开控制台输出的地址即可查看api文档

  > 如果你已经全局安装了apite，可使用 apite init 快速完成以上的安装配置过程哦！

- **脚手架：vite**
  
  ```bash
  # 局部安装依赖
  npm i -D apite
  ```

  ```js
  // vite.config.js
  import { viteExt } from 'apite'

  export default {
    // other config
    configureServer: [ viteExt({ /**ExtConfig*/ }) ]
  }
  ```

- **脚手架：webpack, react, vue**

  ```bash
  # 局部安装依赖
  npm i -D apite
  ```

  ```js
  // webpack.config.js / vue.config.js
  const { webpackExt } = require('apite')

  module.exports = {
    // other config
    devServer:{
      before: webpackExt({ /**ExtConfig*/ })
    }
  }
  ```
  > 使用create-react-app创建的react项目，可以使用下面的配置
  ```js
  // 新建 /src/setupProxy.js，并写入
  const { webpackExt } = require('apite')
  
  module.exports = webpackExt({ /**ExtConfig*/ })
  ```

  > react项目中如果不存在 webpack 配置文件，可以使用 npm run eject 释放  
  > devServer 配置需要在项目中使用 web-dev-server

## 配置参数

  **参数的配置方式有三种：**

  1. 命令行传入，如：

      ```bash
      # eg
      apite --dir=mock --port=8080
      ```
  2. 项目根目录配置文件 apite.config.js，优先级最高
  3. 插件函数中传入

  **所有支持的参数列表**

  ```js
    {
      // 服务端口，命令行模式有效
      port: 3000,
      // 独立配置文件，默认为 apite.config.js
      config: 'apite.config.js',
      // 代理设置
      proxy: null,
      // api所在目录，相对根目录或绝对路径，默认为 api 目录
      dir: 'api',
      // 静态文件目录，相对根目录或绝对路径,如： 'public'
      public: null,
      // 文件兼听延时,为 0 时不兼听
      watchDelay: 300,
      // 是否格式化json输出
      jsonFormat: true,
      // 是否全局mock
      mock: false,
      // 请求地址前辍， 命令行模式默认为空，插件默认为 '/api'
      prefix: '',
      // 是否严格匹配请求类型
      strictMethod: false,
      // 返回数据格式
      defaultType: '',
      // 设置跨域，如: '*'
      crossDomain: '',
      // jsonp请求callback请求参数
      jsonpCallback: 'callback',
      // 文档生成路径，为空时不生成文档
      doc: '/',
      // 文档标题
      docTitle: '',
      // 文档描述，文本或markdown文档地址，相对于根目录
      docDesc: '',
      // 公共返回格式定义
      resp: {
        code: ['code', 0], // 成功字段，默认返回码 
        fail: ['fail', 400], // 失败信息，默认返回码
        msg: ['msg', 'ok'], // 信息字段，默认值 
        result: ['result'], // 结果字段
        total: ['total', 0] // 列表总数字段，默认值
      },
    }
  ```

## 开发api

  在指定的目录 (默认为 api) 新建js文件，引入 apite。

  **注释说明**
  - @sort 文件排序，影响api文档中的模块排序，需要文件在引入apite代码之前写才有效
  - @name 名称，显示在文档页列表中
  - @param 入参，遵循jsdoc规范
  - @description 其他文本为描述信息，支持html标签， @description可省略

  ```js
  /**
   * @sort 1
   * @name 接口示例
   * 接口描述
   */
  const { api, delay, mock, resp } = require('apite')
  ```

  - 返回一个json 

    ```js
    // JSON
    api.get('/json', {
      msg: 'json'
    })
    ```
  - 模拟数据

    ```js
    /**
    * @name 模拟数据
    * 点击在线调试传参数请求看看
    * @param {string} name 名称
    * @param {number} [age=10] 年龄
    * @param {boolean} [online=true] 是否在线
    */
    api.post('/post', ctx=>{
      return mock({
        id: '@id',
        number: '@int(5,9)',
        name: '@name',
        cname: '@cname',
        date: '@dateTime',
        reg: /\w{10}/
      })
    })
    ```

  - 更多写法请参考 [官网示例](https://apite.frp.boyxing.com/)

## 未来功能规划
  - 在web页面可直接添加修改接口，并直接写入到文件
  - 在线调试增加复制前后端代码的功能
  - 源码中的web服务考虑使用用koa的洋葱模型实现
    
## 版本更新

### v1.2.0 
- 在线调试支持get,post参数同时提交
- 除get请求外，其他类型请求默认定位到请求体tab
- 调试重置小图标更换
- resp公共返回类支持格式定义

### v1.1.3 
- 支持cookie，ctx.cookie.name 获取，ctx.setCookie() 设置
- 支持路由参数，如 /user/{id}.html，ctx.params.id 获取
- 文档页添加版本号




  

  





