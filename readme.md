# just-api [![translate-svg]][translate-list]

[translate-svg]: http://llever.com/translate.svg
[translate-list]: https://github.com/chinanf-boy/chinese-translate-list


「 是一个声明性的,基于规范的测试`REST`,`GraphQL`API的框架 」

[中文](./readme.md) | [english](https://github.com/kiranz/just-api)


---

## 校对 🀄️

<!-- doc-templite START generated -->
<!-- repo = 'kiranz/just-api' -->
<!-- commit = '975495748208e959b86b81b3cb0baba4d7da0ee5' -->
<!-- time = '2018 8.27' -->
翻译的原文 | 与日期 | 最新更新 | 更多
---|---|---|---
[commit] | ⏰ 2018 8.27 | ![last] | [中文翻译][translate-list]

[last]: https://img.shields.io/github/last-commit/kiranz/just-api.svg
[commit]: https://github.com/kiranz/just-api/tree/975495748208e959b86b81b3cb0baba4d7da0ee5

<!-- doc-templite END generated -->

- [ ] readme.md
- [ ] Home: [index.md](./docs/docs/index.md)
- [ ] 入门: [getting-started.zh.md](./docs/docs/getting-started.zh.md)
- [ ] 基本 概念: [basic-concepts.zh.md](./docs/docs/basic-concepts.zh.md)
- [ ] CLI 参考: [CLI.zh.md](./docs/docs/CLI.zh.md)
- [ ] 功能: [features.zh.md](./docs/docs/features.zh.md)
- [ ] 并行 运行: [running-suites-in-parallel.zh.md](./docs/docs/running-suites-in-parallel.zh.md)
- [ ] 报告: [reporters.zh.md](./docs/docs/reporters.zh.md)
- [ ] 示例: [examples.zh.md](./docs/docs/examples.zh.md)

### 贡献

欢迎 👏 勘误/校对/更新贡献 😊 [具体贡献请看](https://github.com/chinanf-boy/chinese-translate-list#贡献)

## 生活

[If help, **buy** me coffee —— 营养跟不上了，给我来瓶营养快线吧! 💰](https://github.com/chinanf-boy/live-need-money)

---

### 目录

<!-- START doctoc -->
<!-- END doctoc -->


# just-api

[![npm package](https://nodei.co/npm/just-api.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.com/package/just-api)

[![Join the chat at https://gitter.im/just-api/Lobby](https://badges.gitter.im/just-api/Lobby.svg)](https://gitter.im/just-api/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) 
[![Twitter](https://img.shields.io/twitter/follow/just_api_.svg?style=social&label=Follow)](https://twitter.com/intent/follow?screen_name=just_api_)

API是一个声明性的ㄡ基于规范的测试框架. `REST`,`GraphQL`API. 用户可以在不编写代码的情况下测试API,但它们也可以在需要时使用代码. 它从YAML文件读取API测试规范,并以串行/并行模式运行它们. 测试报告可以以多种格式生成,包括HTML和JSON. <br>

简单来说,用户通过在YAML文件中提供一组请求和响应验证规范来构建测试套件. 每个套件都可以有一个或多个规格. API只需构建请求,将其发送到服务器并根据规范验证响应. 可以选择验证以下任一或全部

-   状态码
-   报头
-   曲奇饼
-   响应JSON体
-   响应JSON模式

*或提供自定义的JavaScript函数来验证响应*

找到更多[在这里](http://kiranz.github.io/just-api/)
<br>

## 链接

-   [文档](http://kiranz.github.io/just-api/)
-   [格斗聊天室](https://gitter.im/just-api/Lobby)
-   [谷歌集团](https://groups.google.com/forum/#!forum/just-api)
-   [问题跟踪器](https://github.com/kiranz/just-api/issues)

## 特征

-   并行/串行模式下运行测试套件
-   支持所有广泛使用的HTTP方法
-   支持X-WWW格式URL编码请求ㄡ多部分请求ㄡ文件上传
-   内置的响应验证构造 (头文件ㄡcookieㄡ状态代码ㄡJSON体ㄡJSON模式) 
-   自定义响应验证器功能
-   支持运行自定义内联或模块JavaScript同步/异步功能
-   支持钩子 (在所有之前ㄡ在每个之前ㄡ之后ㄡ在测试之前ㄡ在测试之后) 
-   自定义套件配置
-   链式请求流
-   在运行时定义/重写请求路径ㄡ查询参数ㄡ路径参数ㄡ标头ㄡ正文
-   用于重用的套件和测试上下文
-   支持从一个或多个测试套件导入规范
-   组内与套房间依赖关系
-   复用测试规范
-   重试失败测试
-   循环: 用列表生成"n"测试数
-   内置的HTML,JSON记者
-   可以为同一运行生成多种格式的报表
-   为失败测试记录HTTP请求/响应数据
-   正确的错误报告
-   可以运行与给定模式/字符串匹配的测试
-   跳过规格测试
-   禁用或启用重定向
-   报告测试持续时间
-   允许用户插入自定义记者

[查看所有特征](https://kiranz.github.io/just-api/features/)

## 入门

> 只需运行API,就需要No.jsV7.1.0或更新. 

### 安装

```sh
$ npm install just-api
```

下面是一个简单的例子,展示了API的用法. 

```sh
$ mkdir specs
$ vim specs/starwars_service.yml
```

在编辑器中写入以下套件

```yaml
meta:
  name: Star Wars suite
configuration:
  scheme: https
  host: swapi.co
  base_path: /api
specs:
  - name: get Luke Skywalker info
    request:
      path: /people/1/
      method: get
    response:
      status_code: 200
      headers:
        - name: content-type
          value: !!js/regexp application/json     
      json_data:
        - path: $.name
          value: Luke Skywalker
```

回到终端

```sh
$ ./node_modules/.bin/just-api

   ✓ get Luke Skywalker info (1216ms)

  Done: specs/starwars_service.yml (Passed)

0 skipped, 0 failed, 1 passed (1 tests)
0 skipped, 0 failed, 1 passed (1 suites)
Duration: 1.3s
```

### GAPQL API测试

下面的示例测试一个返回给定IP地址的位置的图形化API. 

创建一个YAML套件,只运行API. 

```yaml
meta:
  name: GraphQL location service
configuration:
  host: api.graphloc.com
  scheme: https
specs:
  - name: Get Location of a given ip address
    request:
      method: post
      path: /graphql
      headers:
        - name: content-type
          value: application/json
      payload:
        body:
          type: json
          content:
            query: >
                   {
                    getLocation(ip: "8.8.8.8") {
                      country {
                        iso_code
                      }
                     }
                    }
            variables: null
            operationName: null
    response:
      status_code: 200
      json_data:
        - path: $.data.getLocation.country.iso_code
          value: US
```

### 带有钩子和自定义验证的链式请求流

当需要测试复杂的链式API流时,在钩子中运行依赖项以获取必要的数据并将其传递给实际测试. 

下面的示例演示如何使用钩子ㄡ获取数据和使用自定义验证器函数验证响应. 

```yaml
meta:
  name: Starwars suite
configuration:
  scheme: https
  host: swapi.co
  base_path: /api
specs:
  - name: get R2-D2 info
    request:
      path: /people/3/
      method: get
    response:
      status_code: 200
      json_data:
        - path: $.name
          value: R2-D2

  - name: search R2-D2 info
    before_test:
      run_type: inline
      inline:
        function: !js/asyncFunction >
          async function() {
            var response = await this.runSpec('get R2-D2 info');
            var jsonData = JSON.parse(response.body);
            this.test.query_params = { name:  jsonData.name };
          }
    request:
      path: /people
      method: get
    response:
      status_code: 200
      custom_validator:
        run_type: inline
        inline:
          function: !!js/function >
            function() {
              var jsonData = JSON.parse(this.response.body);
              var r2d2 = jsonData.results.find(result => result.name === 'R2-D2');
              
              if (!r2d2) 
                throw new Error('R2-D2 not returned in search results');
            }
```

注意: 您还可以将自定义JS函数放置在模块中,并指定要导入的YAML中的函数名ㄡ模块路径. 

更先进的东西可以用API来完成. 文件说明了一切. 看一看[just-api网站](http://kiranz.github.io/just-api/)详细的文档. 

如果您希望使用DOCKER运行API,那么您可能需要签出API DOCKER样板. [在这里](https://github.com/kiranz/docker-just-api-sample)

## 保持器

Kiran Kiang.6D.Hel@ Gmail网站

## 许可证

[MIT许可](https://github.com/kiranz/just-api/blob/master/LICENSE)

## 工具书类

-   [just-api,测试基于HTTP的API (REST,GAPQL) ](https://medium.com/@kiranz.m/testing-http-based-apis-rest-graphql-f215fba989ca)
