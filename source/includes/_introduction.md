# 介绍


## 接口地址

- Dev: [https://graphql-dev.shoppo.com/api/wise](https://graphql-dev.shoppo.com/api/wise)
- Prod : [https://graphql.shoppo.com/api/wise](https://graphql.shoppo.com/api/wise)
- 调用方式: `https`
- 编码格式: `utf-8`
- 接口验证: `accesstoken`


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

