# 介绍


## 接口地址

- Dev: [https://graphql-dev.shoppo.com/api/wise](https://graphql-dev.shoppo.com/api/wise)
- Prod : [https://graphql.shoppo.com/api/wise](https://graphql.shoppo.com/api/wise)
- Gateway(新增API网关): [http://tracking-gateway.shoppo.com](http://tracking-gateway.shoppo.com)
- 调用方式: `https`
- 编码格式: `utf-8`
- 接口验证: `accesstoken`
- 测试环境 `accesstoken`: `6146211cb3c54a5999b78f4df1f88b63`


> curl请求(get)

```shell
$ curl -s https://graphql-dev.shoppo.com/api/wise/health_check -H "accesstoken: <Your access token>"
```

> 调用成功

```json
Go Shoppo
```

> 调用失败

```json
{
  "errors": [
    {
      "display_to_user": false,
      "message": "Unauthorized request",
      "type": "AccessDenied"
    }
  ]
}
```


## POST 请求

- content-type: `application/json`
- requst-body: `json` 字符串
- 返回结果: `json`

