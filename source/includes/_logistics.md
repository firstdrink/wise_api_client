# 物流


## 流程图

![](./images/wise.png)


## 下载物流面单


### `/api/wise/logistics/stamp`

- 传商品信息，称重数据返回物流面单
- 请求方式: `POST`


> curl请求

```shell
$ curl https://graphql-dev.shoppo.com/api/wise/logistics/stamp -H "Content-Type:application/json" -H "accesstoken: 6146211cb3c54a5999b78f4df1f88b63" -X POST --data '{"order_items":[{"tracking_number":"Z6y2yDEZpoaFa6","weight":2.2},{"tracking_number":"aKgRgDZ6vnvug1","weight":1.1}],"order_id":"NKozBgz8gvqCoY", "has_battery": true}'

```

### json请求参数说明:

名称 | 二级名称 | 类型 | 必填 | 描述
--- | ---- | --- | --- | ---
order_id | | string | 是 | 订单ID
has_battery | | boolean | 是 | 包裹是否带电
order_items | | array | 是 | 订单项列表
            | tracking_number | string | 是 | 包裹面单号 |
            | weight          | float  | 是 | 重量(Kg)   |



> json结果

> status code: 200

```json
{
	"data": {
		"order_id": "NKozBgz8gvqCoY",
		"stamp_url": "https://cdn.shoppo.com/temporary_uploaded_files/75dbe397d3564c1d9f4b46bff4db8da7.pdf",
		"tracking_number": "NKozBgz8gvqCoY"
	}
}
```



### 返回参数说明:

名称 | 二级名称 | 类型 | 必填 | 描述
--- | --- | --- | --- | ---
data | | object | 是 | 
     | stamp_url       | string | 是 | 面单链接     |
     | tracking_number | string | 是 | 合单物流单号 |
     | order_id        | string | 是 | 订单ID       |
## 推送物流轨迹

### `/api/wise/logistics/checkpoints`

- `Wise` 调用 `Shoppo` 接口，推送物流轨迹信息
- 请求方式: `POST`

> curl请求

```shell

$ curl https://graphql-dev.shoppo.com/api/wise/logistics/stamp -H "Content-Type:application/json" -H "accesstoken: <Your access token>" -X POST --data '{"tracking_number":"xap36y3p85Oio0","checkpoints":[{"date":"2016-08-22 00:00:00","status_desc":"小件揽收","courier":"wise-express","status_number":1},{"date":"2016-08-23 00:00:00","status_desc":"小件入仓","courier":"wise-express","status_number":2}]}'

```


### json请求参数说明:

名称 | 二级名称 | 类型 | 必填 | 描述
--- | --- | --- | --- | ----
type |  | int | 是 | 类型 1: 小件, 2: 合单
tracking_number | | string| 是 | 包裹物流单号
checkpoints | | array | 是 | 轨迹列表
    | status_number | int    | 是 | 状态码 1: 小件揽收, 2: 小件入仓, 3: 合单出仓 |
    | status_desc   | string | 否 | 状态说明                                     |
    | date          | string | 是 | 日期时间， 如 2016-08-22 00:00:00            |
    |  remark       | string | 否  | 说明                                           |
