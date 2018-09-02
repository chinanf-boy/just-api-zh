
# 基本概念

本节介绍了一些高级基本概念,这些概念对于理解日常的Just-API使用非常重要. 我们强烈建议您在继续使用Just-API之前阅读此页面. 

## 高级视图

Just-API的主要目的是在没有代码的情况下测试基于HTTP的API,并使API测试变得简单,免费且快速. 

编写API测试规范并告诉Just-API运行它们. API测试规范是用YAML编写的,我们将每个YAML文件称为测试套件,可以选择编写Javascript (使用任何一个) `npm`模块 (如果需要) 来实现自定义逻辑. 

在运行测试套件时,Just-API以并行性作为高优先级编写,因此您的测试执行所花费的时间越少越好. 

## 将测试套件放在一起

测试套件有三个必需部分 -`meta`,`configuration`和`specs`. 

和可选部分`hooks`和`spec_dependencies` (您可以在文档的后续部分中找到有关这些可选部件的更多信息) . 

### 该`meta`部分

在此部分中指定以下套件属性

-   `name` (*必需属性*) 
-   `locate_files_relative` (*可选属性*) 

*名称*[串]\: 给出一个描述套件的名称 (例如: 像微服务名称) 

*locate_files_relative*[布尔]\: 如果套件中提供的文件路径是否与套件路径相关,则告诉Just-API. 如果为false,Just-API将尝试查找与Node进程当前工作目录相关的文件. 如果为true,Just-API将尝试查找与套件路径相关的文件. 默认值为false. 

注意: 此属性值适用于您在套件中提供的每个文件路径. 

### 该`configuration`部分

您可以使用`configuration`部分指定API的主机,协议,端口等. 您还可以提供自定义Javascript函数`custom_configuration`属性,因此在运行时动态配置套件很容易. 

### 该`specs`部分

`specs`section是一个测试列表. 每个测试都包含名称,请求,响应验证规范和一些其他附加属性. 

### 示例套件

星球大战API服务的示例套件如下所示: 

```yaml
meta:
  name: Star Wars service
configuration:
  scheme: https
  base_path: /api
  custom_configuration:
    run_type: inline
    inline:
      function: !!js/function >
        function() {
          this.host = 'swapi.co';
        }  
specs:
  - name: get Luke Skywalker info
    request: 
      path: /people/1/
      method: get
    response:
      status_code: 200
      json_data:
        - path: $.name
          value: Luke Skywalker     
  - name: get all Star Wars Films
    request: 
      path: /films/   
      method: get
    response:
      status_code: 200  
```

**下一个: **

-   查看可用的全套功能[特征](features). 
-   学习关于**记者**在[记者](reporters). 
