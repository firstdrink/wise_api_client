# 物流


## 流程图

![](./images/wise.png)


## 下载物流面单


### `/api/wise/logistics/stamp`

- 传商品信息，称重数据返回物流面单
- 请求方式: `POST`


> curl请求

```shell
$ curl https://graphql-dev.shoppo.com/api/wise/logistics/stamp -H "Content-Type:application/json" -H "accesstoken: <Your access token>" -X POST --data '{"order_items":[{"tracking_number":"l4kbkMvDQQ0Sql","weight":2.2},{"tracking_number":"xkq8qMDR66ktA3","weight":1.1}],"tracking_number":"xap36y3p85Oio0"}'
```

### json请求参数说明:

名称 | 二级名称 | 类型 | 必填 | 描述
--- | ---- | --- | --- | ---
tracking_bumber | | string | 是 | 主物流单号
order_items | | array | 是 | 订单项列表
            | tracking_nbumber | string | 是 | 包裹面单号 |
            | weight           | float  | 是 | 重量(Kg)   |


> json结果

> status code: 200

```json
{
	"data": {
		"stamp_url": "http://cdn.shoppo.com/ temporary_uploaded_files/2d80da072a3e4ea5888f4a4d24549798.pdf",
        "tracking_number": "xap36y3p85Oio0"
	}
}
```



### 返回参数说明:

名称 | 二级名称 | 类型 | 必填 | 描述
--- | --- | --- | --- | ---
data | | object | 是 | 
     | stamp_url | string | 是 | 面单链接 |
     | tracking_number |   string |  是 |   合单物流单号 |


## 推送物流轨迹

### `/api/wise/logistics/checkpoints`

- `Wise` 调用 `Shoppo` 接口，推送物流轨迹信息
- 请求方式: `POST`

> curl请求

```shell

$ curl https://graphql-dev.shoppo.com/api/wise/logistics/stamp -H "Content-Type:application/json" -H "accesstoken: <Your access token>" -X POST --data '{"tracking_number":"xap36y3p85Oio0","checkpoints":[{"courier":"wise-express","location":"Shanghai","status_code": 1,"time":1539178398},{"courier":"wise-express","location":"Thailand", "status_code": 2, "time":1539179398}]}'

```


### json请求参数说明:

名称 | 二级名称 | 类型 | 必填 | 描述
--- | --- | --- | --- | ----
type |  | int | 是 | 类型 1: 小件, 2: 合单
tracking_number | | string| 是 | 包裹物流单号
checkpoints | | array | 是 | 轨迹列表
    | courier  | string | 是 | 物流公司 
    | location | string | 是 | 位置
    | status_code |  int   | 否 | 状态码 1: 小件揽收, 2: 小件入仓, 3: 合单出仓
    | time     |  int   | 是  |  时间戳
