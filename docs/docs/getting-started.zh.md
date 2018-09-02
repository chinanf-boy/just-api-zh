
## 安装

安装时[npm](https://npmjs.org): 

```bash
npm install just-api
```

!note注意要运行just-api,您需要Node.js v7.10.0或更高版本. 

## 入门

```bash
mkdir specs
```

```bash
vim specs/starwars_service.yml
```

在YAML文件中输入以下规范

```yaml
meta:
  name: "Star Wars suite"
configuration:
  scheme: "https"
  host: "swapi.co"
  base_path: "/api"
specs:
  - name: "get Luke Skywalker info"
    request:
      path: "/people/1/"
      method: "get"
    response:
      status_code: 200
      headers:
        - name: "content-type"
          value: !!js/regexp application/json      
      json_data:
        - path: "$.name"
          value: "Luke Skywalker"
```

回到终端

```text
$ ./node_modules/.bin/just-api

   ✓ get Luke Skywalker info (1216ms)

  Done: specs/starwars_service.yml (Passed)

0 skipped, 0 failed, 1 passed (1 tests)
0 skipped, 0 failed, 1 passed (1 suites)
Duration: 1.3s
```

### 测试GraphQL API

下面的示例测试一个GraphQL API,它返回给定ip地址的位置. 

创建一个YAML套件并运行just-api. 

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

### 具有挂钩和自定义验证的链式请求流

当您需要测试复杂的链式API流时,在挂钩中运行依赖项以获取先决条件数据并将其传递给实际测试. 

下面的示例演示如何使用钩子运行依赖项,获取数据并使用自定义验证器函数验证响应. 

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
              
              if (!r2d2) throw new Error('R2-D2 not returned in search results');
            }
```

!note备注您还可以在模块中放置自定义JS函数,并在YAML中指定函数名称和模块路径. 

### 使用docker运行Just-API测试

如果您希望使用Docker运行Just-API,您可能需要检查Just-API docker样板[这里](https://github.com/kiranz/docker-just-api-sample)
