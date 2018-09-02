
# 刚刚API

Just-API是一个声明性的,基于规范的测试框架`REST`,`GraphQL` (或任何基于HTTP的) API. 用户可以在不编写代码的情况下测试API,但他们也可以在需要时使用代码. 它从YAML文件中读取API测试规范,并以串行/并行模式运行它们. 可以以多种格式生成测试报告,包括HTML和JSON. <br>

简单来说,用户通过在YAML文件中提供一组请求和响应验证规范来构建测试套件. 每个套房都有一个或多个规格. Just-API构建请求,将其发送到服务器并根据规范验证响应. 可以选择验证以下任何一个或全部

-   状态代码
-   头
-   饼干
-   响应JSON正文
-   响应JSON架构

*或提供自定义Javascript函数来验证响应*

## 内容

### 文档

-   [入门](getting-started)
-   [基本概念](basic-concepts)
-   [CLI](CLI)
-   [特征](features)
-   [记者](reporters)
-   [例子](examples)

## 在npm找到它

[![npm package](https://nodei.co/npm/just-api.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.com/package/just-api)

## 保持联系

[![Twitter](https://img.shields.io/twitter/follow/just_api_.svg?style=social&label=Follow)](https://twitter.com/intent/follow?screen_name=just_api_)     [![GitHub stars](https://img.shields.io/github/stars/kiranz/just-api.svg?style=social&label=Star)](https://github.com/kiranz/just-api)

如果您遇到Just-API问题,或者有问题,请与gitter上的社区聊天. 

[![Join the chat at https://gitter.im/just-api/Lobby](https://badges.gitter.im/just-api/Lobby.svg)](https://gitter.im/just-api/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[Just-API Google小组](https://groups.google.com/forum/#!forum/just-api)
