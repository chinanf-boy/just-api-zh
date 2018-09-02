
# Just-API功能

## 请求规范

典型的请求规范包括请求方法,路径,标头,查询参数,路径参数和有效负载 (如果适用) . 

### 请求方法

Just-API支持以下HTTP请求方法. 您可以使用任何这些HTTP方法获得请求规范. 

-   得到
-   POST
-   放
-   补丁
-   删除
-   头
-   OPTIONS

### 指定标题,Cookie,查询参数,路径参数

指定请求标头`headers`key作为名称,值对的列表. <br>指定请求cookie到`cookies`key作为名称,值对的列表. <br>指定请求查询参数`query_params`key作为名称,值对的列表. <br>指定请求路径参数`path_params`key作为名称,值对的列表. <br>

带有方法,标题,cookie,查询参数和路径参数的示例测试规范: 

```yaml
  - name: A sample test
    request:
       path: /user/{userId}/posts/{postId}
       method: get
       headers:
         - name: Accept
           value: application/json
       cookies:
         - name: uid
           value: 399do284kds32odh28 
       path_params:
          - name: userId
            value: 12876
          - name: postId
            value: 2
       query_params:
          - name: limit
            value: 10
    response:
       status_code: 200
```

### 请求机构规格

当您需要发送POST,PUT和PATCH请求的请求主体时,您可以指定主体`payload`键. 

您需要指定`content-type`标题和正文`type`如下所示以下是如何使用POST请求发送JSON正文

```yaml
  - name: create a new user
    request: 
      path: /users
      method: post
      headers:
        - name: content-type
          value: application/json
      payload:
          body:
            type: json
            content:
              firstName: john
              lastName: doe
    response:
      status_code: 201
```

您还可以从文件中将二进制数据作为正文发送,如下所示. 

```yaml
  - name: post binary data (file) as body
    request:
      path: /uploadImage
      method: post
      headers:
        - name: content-type
          value: image/png
      payload:
          body:
            type: binary 
            content: static/assets/image.png # Image path
    response:
      status_code: 200
```

请注意,如果设置,图像路径应该相对于节点进程的当前工作目录或相对于套件的路径`locate_files_relative`真实的`meta`套房的一部分. 

查看有关如何指定请求正文的更多示例[这里](https://github.com/kiranz/just-api/blob/master/test/cli/src/suites/postrawbody.suite.yml)

### x-www-form-urlencoded,多部分请求,文件上传

Just-API支持`x-www-form-urlencoded`和`multipart/form-data`请求,您可以使用该请求上载文件并对复杂请求执行测试. 

#### **指定`x-www-form-urlencoded`请求**

创建一个`x-www-form-urlencoded`请求测试,您需要指定`content-type`标题和表单体如下: 

注意`payload`应该有一把钥匙`form`它应该包含键值对中的表单数据. 

```yaml
  - name: Authenticate a user
    request:
      path: /authenticate
      method: post
      headers:
        - name: content-type
          value: application/x-www-form-urlencoded
      payload:
          form:
            userName: john.doe
            password: john.doe.password
    response:
      status_code: 200
```

更多例子`x-www-form-urlencoded`可以找到测试[这里](https://github.com/kiranz/just-api/blob/master/test/cli/src/suites/urlencodeformpost.suite.yml)

#### **指定`multipart/form-data`请求**

以下是指定多部分请求的方法. 

注意`payload`应该有一把钥匙`form_data`它应该包含表单数据字段列表. 

```yaml
  - name: post multipart form data - single file and field
    request:
      path: /imageNText
      method: post
      headers:
        - name: content-type
          value: multipart/form-data
      payload:
          form_data:
            - name: image_name
              content: static/assets/logo.png # file path
              type: file
            - name: field1
              content: value1
              type: text
    response:
      status_code: 200
```

更多例子`multipart/form-data`可以找到测试[这里](https://github.com/kiranz/just-api/blob/master/test/cli/src/suites/multipartformpost.suite.yml)

## 响应验证

Just-API允许您在不编写任何代码的情况下验证响应,您可以通过指定要验证的内容在响应中验证以下内容. 

-   状态代码
-   头
-   饼干
-   JSON模式
-   JSON正文

您还可以使用自定义验证程序函数来实现自定义验证逻辑. 

### 状态代码验证

提供`status_code`响应中的属性和Just-API根据规范匹配响应的状态代码. 当实际响应的状态代码与指定值不匹配时,测试将失败. 

一个样品: 

```yaml
  - name: get users
    request:
      path: /users
      method: get
    response:
      status_code: 400
```

### 标头验证

当您在响应中指定标头时,Just-API将根据您的规范验证响应标头. 

关于如何编写标头验证的示例. 

```yaml
  - name: get users
    request:
      path: /users
      method: get
    response:
      headers:
        - name: content-type
          value: application/json
```

您甚至可以将标头与正则表达式匹配而不是精确值,如下所示: 

遵循响应规范将检查响应头*内容类型*匹配正则表达式模式*'json'*. 

```yaml
  - name: get users
    request:
      path: /users
      method: get
    response:
      headers:
        - name: content-type
          value: !!js/regexp /json/
```

可以找到有关响应头验证的更多示例[这里](https://github.com/kiranz/just-api/blob/master/test/cli/src/suites/headers.suite.yaml)

### Cookies验证

当您在响应中指定cookie时,Just-API将根据您的规范验证响应cookie. 

关于如何编写cookie验证的示例. 

```yaml
  - name: get users
    request:
      path: /users
      method: get
    response:
      cookies:
        - name: uid
          value: xxxxxx
```

您甚至可以将cookie值与正则表达式匹配,而不是精确值,如下所示: 

遵循响应规范将检查响应cookie*uid*匹配正则表达式模式*/[0-9]+ /*. 

```yaml
  - name: get users
    request:
      path: /users
      method: get
    response:
      cookies:
        - name: uid
          value: !!js/regexp /[0-9]+/
```

可以找到有关响应cookie验证的更多示例[这里](https://github.com/kiranz/just-api/blob/master/test/cli/src/suites/cookies.suite.yml)

### 响应JSON模式验证

有时您可能希望验证响应正文中收到的JSON模式,这样可以确保服务器正在发送与您期望的完全相同的数据. 

使用Just-API,您可以通过指定来验证响应JSON模式`json_schema`属性作出回应. 

以下示例显示如何从文件中读取预期的架构并验证响应JSON正文. 这里json_schema有两个字段,`type`key可以是文件也可以是内联. 什么时候`type`是文件,您需要提供架构文件的相对路径`$ref`键. 

```yaml
  - name: get users
    request: 
      path: /users
      method: get
    response:
      json_schema:
          type: file
          $ref: static/schema/expected_schema_for_users.json #path to the expected schema file
```

您还可以通过将type设置为,在YAML中指定预期的JSON模式`inline`并指定一个字符串`$ref`如下: 

```yaml
  - name: get users
    request: 
      path: /users
      method: get
    response:
      json_schema:
          type: inline
          $ref: >
                {
                  "$id": "http://example.com/example.json",
                  "type": "object",
                  "$schema": "http://json-schema.org/draft-06/schema#",
                  "properties": {
                    "firstName": {
                      "$id": "/properties/firstName",
                      "type": "string"
                    }
                  }
                }
```

如果您不确定JSON架构标准,请参阅[JSON模式](http://json-schema.org/)

可以找到有关响应JSON模式验证的更多示例[这里](https://github.com/kiranz/just-api/blob/master/test/cli/src/suites/jsonschema.suite.yml)

### 响应JSON正文验证

如果要验证响应JSON的一个或多个字段,可以通过提供来实现`json_data`作为响应规范的一部分. 

`json_data`应该有一个路径和值对的列表,其中path告诉Just-API如何定位字段,value是该字段的预期值. 

以下示例显示. 

```yaml
  - name: get users
    request: 
      path: /users
      method: get
    response:
      json_data:
        - path: $.[0].firstName
          value: john
```

如果您不确定如何生成JSON路径,请参阅[JSON路径](https://github.com/dchester/jsonpath)

有关响应的更多示例可以找到JSON数据验证[这里](https://github.com/kiranz/just-api/blob/master/test/cli/src/suites/jsondata.suite.yml)

### 用户定义的自定义响应验证器功能

当您需要使用自定义逻辑验证响应时,可以通过提供一个来实现`inline`要么`module`自定义javascript函数`custom_validator`属性. 

Just-API将通过将响应传递给函数的上下文来调用自定义函数,因此您可以使用来访问响应`this.response`. 

当您需要更多响应细节时,`this.response`还将具有以下属性. 

-   身体
-   的StatusCode
-   状态消息
-   头
-   身体
-   持续时间

以下是有关如何指定自定义内联Javascript函数以验证响应的示例

```yaml
  - name: get users
    request:
      path: /users
      method: get
    response:
      status_code: 200
      custom_validator:
        run_type: inline
        inline:
          function: !!js/function >
            function() {
              var body = JSON.parse(this.response.body);
              
              if (body.users[0].firstName !== 'john')
                  throw new Error("first user's name is not john");
            }
```

您还可以通过提供指定在模块中定义和导出的自定义函数`run_type`作为模块和模块路径如下. 

```yaml
  - name: get users
    request:
      path: /users
      method: get
    response:
      status_code: 200
      custom_validator:
        run_type: module
        module:
          module_path: modules/custom_module.js # module path
          function_name: validateUsersResponse
```

可以找到有关使用自定义函数验证响应的更多示例[这里](https://github.com/kiranz/just-api/blob/master/test/cli/src/suites/customvalidator.suite.yml)

## 自定义Javascript函数

Just-API允许用户为 (钩子和自定义验证器) 指定自定义Javascript函数. 

这些自定义Javascript函数可以在模块中内联或定义. 它们可以是同步的或异步的 (使用async关键字定义) . 

### Cutom内联和模块功能

#### 自定义内联函数

一个`inline`同步功能定义如下. 

```yaml
  custom_validator:
    run_type: inline
    inline:
      function: !!js/function >
        function() {
          // some custom code here...
        }
```

请注意,run_type是`inline`和函数映射到的函数键`inline`属性. 用标记`!!js/function`就是如何告诉Just-API解析器将其视为自定义同步函数. 

#### 自定义模块功能

一个`module`同步功能如下指定. 

```yaml
 custom_validator:
   run_type: module
   module:
     module_path: modules/custom_module.js
     function_name: customFunctionName
```

请注意,run_type是`module`和功能细节映射到`module`属性. `module_path`从Node的进程的当前工作目录或相对于套件的路径 (如果设置) 具有JS文件的相对路径`locate_files_relative`真实的`meta`套房的一部分. JS文件应该是标准的Node.js模块. `function_name`是模块中导出的函数. 

### 具有promise的自定义函数的异步支持

Just-API不支持回调机制,使用promises包装async opeartions并返回promise. 

自定义函数可以使用async关键字进行异步定义,Just-API会等待,直到async函数返回的promise被解析或拒绝. 

一个`inline`异步函数定义如下. 

```yaml
  custom_validator:
    run_type: inline
    inline:
      function: !js/asyncFunction >
        async function() {
          // some custom asynchrounous code here...
        }
```

请注意,run_type是`inline`和函数映射到的函数键`inline`属性. 用标记`!js/asyncFunction`就是如何告诉Just-API解析器将其视为自定义异步函数. 

一个`module`异步函数如下所示. 

```yaml
 custom_validator:
   run_type: module
   module:
     module_path: modules/custom_module.js
     function_name: customAsyncFunctionName
```

请注意,run_type是`module`和功能细节映射到`module`属性. `module_path`从Node的进程的当前工作目录或相对于套件的路径 (如果设置) 具有JS文件的相对路径`locate_files_relative`真实的`meta`套房的一部分. JS文件应该是标准的Node.js模块. `function_name`是使用async关键字定义并在模块中导出的异步函数. 

## 套件配置

在运行测试时,您需要根据要测试的环境配置API主机,端口等. 

为了方便这一点,Just-API提供了一个`configuration`每个套房的部分,你可以在那里`custom_configuration`属性并提供自定义JS函数来设置套件配置. 

这是使用内联自定义JS函数执行此操作的示例. 

```yaml
configuration:
  scheme: https
  custom_configuration:
    run_type: inline
    inline:
      function: !!js/function >
        function() {
          if (process.env.TEST_ENVIRONMENT === 'Dev') {
             this.host = 'dev-host.com';
          }
          
          if (process.env.TEST_ENVIRONMENT === 'QA') { 
            this.host = 'qa-host.com';
          }
        }
```

您还可以使用基于模块的自定义JS函数来配置套件. 

注意`custom_configuration`是可选的. 如果测试环境详细信息没有为您更改,您始终可以指定静态套件配置. 

```yaml
configuration:
  scheme: https
  host: testhost.com
  port: 8000
  base_path: /api
```

查看更多示例[这里](https://github.com/kiranz/just-api/tree/master/test/cli/src/suites/suiteconfig)

## 钩

Just-API支持以下钩子,因此很容易管理测试. 

**套房专用挂钩**

这些可以指定为`hooks`属性. 

-   之前 (在套件中的所有规格之前运行) 
-   毕竟 (在套件中的所有规格之后运行) 
-   每个之前 (在套件中的每个规范之前运行) 
-   每个之后 (在套件中的每个传递规范之后运行) 

**测试特定的钩子**

可以根据需要为每个测试指定这些. 

-   在测试之前 (在规范之前运行,您可以使用此钩子来设置测试先决条件) 
-   经过测试 (如果规范通过,则在规范之后运行,您可以使用此钩子来拆除/清除任何测试特定数据) 

查看一些钩子样本[这里](https://github.com/kiranz/just-api/tree/master/test/cli/src/suites/hooks)

## 动态请求构建

始终无法对请求进行静态规范. 有时,测试依赖于另一个请求返回的数据,您希望首先运行依赖项规范并从中获取所需数据并将其传递给当前测试请求. 为了处理这样的用例,Just-API提供了一个`before_test`每个测试都有钩子. 使用此挂钩,您可以运行测试先决条件并更新实际测试的请求数据. 

通常,当请求取决于从另一个请求接收的数据时,我们需要构建动态请求. 在这种情况下,您可以在before_test钩子中运行依赖项规范,并使用响应来构建实际的测试请求. 

### 头

更新或覆盖请求标头`before test`钩: 

```yaml
  - name: send headers specified in before test hook
    before_test:
      run_type: inline
      inline:
        function: !!js/function >
          function() {
            this.test.headers = { Authorization: 'some token' };
          }
    request:
      path: /users
      method: get
    response:
      status_code: 200
```

### 饼干

更新或覆盖请求cookie`before test`钩: 

```yaml
  - name: send cookies specified in before test hook
    before_test:
      run_type: inline
      inline:
        function: !!js/function >
          function() {
            this.test.cookies = { uid: 'some token' };
          }
    request:
      path: /users
      method: get
    response:
      status_code: 200
```

### 查询参数

更新或覆盖请求查询参数`before test`钩: 

```yaml
  - name: Query params added in hook
    before_test:
      run_type: inline
      inline:
        function: !!js/function >
          function() {
            this.test.query_params = { limit: 10 };
          }
    request:
       path: /users
       method: get
    response:
       status_code: 200
```

### 路径参数

更新或覆盖请求路径参数`before test`钩: 

```yaml
  - name: path params added in hook
    before_test:
      run_type: inline
      inline:
        function: !!js/function >
          function() {
            this.test.path_params = { id: 1 };
          }
    request:
       path: /user/{id}
       method: get
    response:
       status_code: 200
```

### 身体

您还可以使用以下钩子在运行时定义请求有效负载: 

```yaml
  - name: Payload defined in hook
    before_test:
      run_type: inline
      inline:
        function: !!js/function >
          function() {
            this.test.payload = { body: {type: 'json', content: { firstName: 'john',
             lastName: 'doe'}} };
          }
    request:
       path: /users
       method: post
       headers:
         - name: content-type
           value: application/json
    response:
       status_code: 201
```

## 自定义上下文

您可以在钩子中访问套件上下文和测试上下文,因此您可以将它们用作存储自定义数据的方法,以便在以后的钩子中使用. 

可以使用钩子访问套件上下文`this.suite`. 可以使用'this.test'在测试特定的钩子中访问测试上下文. 

### 将套件和测试数据存储在上下文中以供重用

如果您希望稍后在某些测试中使用某些数据,则可以将数据存储在套件上下文中`before_all`钩. 

```yaml
  before_all:
    run_type: inline
    inline:
      function: !!js/function >
        function() {
           this.suite.serviceSecret = 'secret';
        }
```

以后当你在测试中需要秘密时,你可以在before_test钩子中访问它. 

```yaml
  - name: using suite context in before test hook
    before_test:
      run_type: inline
      inline:
        function: !!js/function >
          function() {
              this.test.headers = { Authorization: this.suite.serviceSecret };
          }
    request:
      path: /users
      method: get
    response: 
      status_code: 200
```

同样,您可以在before_test hook中使用设置测试上下文`this.test`并在after_test钩子中使用\`this.test'访问它. 

## 规范依赖

Just-API允许在流中测试API,其中一个请求依赖于来自另一个请求的响应数据. 在测试复杂的事务API时,这非常有用. 

### 运行依赖项

可以使用挂钩来运行先决条件请求. 有`before_each`和`before_test`挂钩. 

如果您具有特定于测试的依赖项,那么您可能希望使用`before_test`钩子来运行dependecy规范. 

支持2种类型的依赖项,`Intrasuite`&`Intersuite`规范. 

### Intrasuite规范依赖

Intrasuite依赖是指您具有在同一套件中指定的依赖项. 然后,您可以使用运行依赖项`this.runSpec()`在钩子里. runSpec函数有2个参数. 第一个参数是依赖项规范的名称,第二个参数是一个对象,其中包含要传递给请求的其他信息,如标题,查询参数,路径参数和正文. 

以下一组规范显示了如何将依赖项作为测试的一部分运行,并将数据用于后续请求. 

```yaml
specs:
  - name: this is the dependency spec
    enabled: false
    request:
      path: /token
      method: get
    response:
      status_code: 200

  - name: actual test spec
    before_test:
      run_type: inline
      inline:
        function: !js/asyncFunction >
          async function() {
            var response = await this.runSpec('this is the dependency spec');
            var tokenResponse = JSON.parse(response.body);
            this.test.headers = { Authorization:  tokenResponse.token };
          }
    request:
      path: /users
      method: get
    response:
      status_code: 200
```

### Intersuite spec依赖项

Intersuite依赖项是指您在另一个套件中指定的依赖项,但您使用它进行导入`spec_dependencies`构造. 

运行Intersuite依赖关系类似于运行Intrasuite依赖关系. 

您可以通过提供套件的路径来导入另一个套件中定义的规范`spec_dependencies`. 

```yaml
spec_dependencies:
  - suites/anothersuite.yml 
```

如果设置,Path相对于Node进程的当前工作目录或相对于suite的路径`locate_files_relative`真实的`meta`套房的一部分. 

您可以通过列出其路径从一个或多个套件导入规范. 

请注意,这些导入的规范仅在您使用时才可用`this.runSpec`. 

请查找有关依赖项的一些示例[这里](https://github.com/kiranz/just-api/blob/master/test/cli/src/suites/suitedependencies/intersuitedeps.suite.yml)

## 跳绳

您可以使用规范明确跳过套件和测试. 

### 跳过套房

如果目录中有套件,但由于某种原因想要跳过它,则可以通过提供来实现`false`作为启用密钥的值`meta`在套件中,Just-API将跳过套件. 

```yaml
 meta:
  name: Disabled suite
  enabled: false
configuration:
  scheme: http
  host: 127.0.0.1
specs:
  - name: get users
    request:
      path: /users
      method: get
    response:
      status_code: 200
```

请注意,如果在Just-API读取之前发生任何错误`meta`套件的信息,然后套件将被标记为失败. 

### 跳过测试

如果套件中有一组规格,但想要跳过规范,则可以通过提供来实现`false`作为规范的启用键的值. 

```yaml
  - name: disabled spec
    enabled: false
    request:
      path: /
      method: get
    response:
      status_code: 200
```

Just-API会将规范标记为跳过的测试. 

## 重用测试规范

除了可读性之外,关于YAML的一个很棒的事情是你可以重用规范的一部分. 

参考[干](https://blog.daemonl.com/2016/02/yaml.html)看看如何在YAML中重用东西. 

您可以在下面看到如何重用响应规范`&`和`*`

```yaml
meta:
  name: suite name
configuration:
  scheme: http
  host: example.com
specs:
  - name: get user1
    request:
      path: /users/1
      method: get
    response: &default_response
      status_code: 200

  - name: get user2
    request: &default_request
      path: /users/1
      method: get
    response: *default_response
```

## 重试失败的测试

有时,资源需要一些时间才能达到您期望的状态,例如,当您轮询作业状态并且它在一段时间后进入完成状态时. 在这种情况下,您可能希望重试几次同样的请求. 

如果您看到下面的示例,响应规范说明了200状态代码. 如果响应验证失败,那么如果您指定了a,则Just-API会尝试重试该请求`retry`属性. 这里将尝试3次请求`count`每次尝试前等待10ms. 

`wait_before_each`是一个可选属性,用于指定每次尝试之前要等待的毫秒数. 

```yaml
  - name: retry example
    request:
      path: /retryPath
      method: get
    retry:
      count: 3
      wait_before_each: 10
    response:
      status_code: 200
```

## 循环

您可以使用循环结构循环遍历项目列表,每个项目都会生成测试. 

可以事先在spec中指定项目列表,也可以指定返回项目列表的自定义JS函数. 

这是如何指定静态循环,`type`是静态的,循环列表映射到`static`字段作为项目列表. 您可以通过访问访问before_test钩子中的该项目`this.loopItem`. 

```yaml
  - name: static loop test
    loop:
      type: static
      static:
        - 2
        - 3
    before_test:
      run_type: inline
      inline:
        function: !!js/function >
          function() {
            this.test.query_params = { limit : this.loopItem };
          }
    request:
      path: /users
      method: get
    response:
      status_code: 200
```

还有另一种提供项目列表的方法`dynamic`类型和自定义JS函数. 

```yaml
  - name: dynamic loop test
    loop:
      type: dynamic
      dynamic:
        run_type: inline
        inline:
          function: !!js/function  >
            function() {
              return [2, 3];
            }           
    before_test:
      run_type: inline
      inline:
        function: !!js/function >
          function() {
            this.test.query_params = { limit : this.loopItem };
          }           
    request:
      path: /users
      method: get
    response:
      status_code: 200
```

请注意,如果您的循环包含n个项目的列表,则将在运行时生成n个测试. 

## 附加功能

以下是Just-API提供的一些附加功能. 

### 报告测试持续时间

Just-API报告所有报告格式的每次测试运行的持续时间. 

### 仅运行与给定模式/文本匹配的测试

如果您只想运行具有给定文本或模式的规格匹配,则可以使用`--grep`选项. 

调用之后将运行名称与"user"字符串匹配的所有规范. 

```sh
./node_modules/.bin/just-api --grep user specs
```

### 其他请求选项

支持以下附加请求选项. 

-   followRedirect  - 将HTTP 3xx响应作为重定向. 
-   followAllRedirects  - 遵循非GET HTTP 3xx响应作为重定向. 
-   followOriginalHttpMethod  - 默认情况下我们重定向到HTTP方法GET. 您可以启用此属性以重定向到原始HTTP方法 (默认值: false) 
-   encoding  - 用于响应数据的setEncoding的编码. 如果为null,则将body作为Buffer返回. 其他任何东西 (包括默认值undefined) 将作为encoding参数传递给toString ()  (默认情况下这实际上是utf8) .  (注意: 如果您需要二进制数据,则应设置encoding: null. ) 
-   gzip  - 如果为true,则添加Accept-Encoding标头以从服务器请求压缩内容编码 (如果尚未存在) 并在响应中解码支持的内容编码. 

有关如何指定这些选项的示例规范: 

```yaml
  - name: spec with redirection disabled
    request: 
      path: /home
      method: get
      additional_options:
        followRedirect: false
        gzip: true
    response:
      status_code: 200
```

### 递归地查找套件

如果要运行存储在跨越多个嵌套级别的目录层次结构中的套件,可以使用调用Just-API`--recursive`选项. 

```sh
./node_modules/.bin/just-api --recursive specs
```

### 正确的错误报告

当测试或套件失败时,Just-API会提供正确的错误,导致报告失败. 

### 退出CI支持代码

Just-API以适当的退出代码退出,因此您可以在CI中使用它来确定测试的状态. 

除非发生一些意外错误,否则退出代码通常等于失败套件的数量. 

### 记录失败测试的HTTP请求/响应数据

当具有多个依赖项的测试失败时,很难跟踪哪个请求失败. 为了简化故障跟踪,Just-API允许您为失败的测试请求HTTP呼叫详细信息. 您将能够在HTML或JSON报告中看到针对失败测试所做的所有HTTP调用. 

要启用此功能,您需要调用Just-API`--reporter-options logRequests`

```sh
./node_modules/.bin/just-api --reporter html --reporter-options logRequests specs
```

### 没有回调

你可能已经观察到在任何地方都没有提到回调. 是的,Just-API在运行自定义JS函数时不支持回调机制. 这是为了鼓励使用promises进行异步操作. 

在处理异步任务时,将它们包装在异步函数中并返回一个promise. 

## 并行运行套件

看到[页](running-suites-in-parallel) 

## 记者

看到[页](reporters) 
