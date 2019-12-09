# 物流V2


## 小包入库


![](./images/wise2.1.png)

###  `/api/wise/logistics/inbound`

- 传小包原单号数组、重量、安检信息
- 请求方式: `POST`

### json请求参数说明

名称 | 二级名称 | 类型 | 必填 | 描述
--- | ---- | --- | --- | ----
pickup_info | | object | 是 |
            | has_liquid                | boolean | 是 | 是否液体   |
            | has_battery               | boolean | 是 | 是否带电   |
            | has_sensitive             | boolean | 是 | 是否违禁   |
            | weight                    | float   | 是 | 重量       |
            | pickup_time               | string  | 是 | 入库时间   |
            | logistics_tracking_number | string  | 是 | 小件单号   |
            | oversize                  | boolean | 否 | 是否超尺寸 |
            | length                    | float   | 否 | 长(cm)     |
            | width                     | float   | 否 | 宽(cm)     |
            | height                    | float   | 否  | 高(cm)       |
carrier_code | | string | 是 | 渠道号 |


> 参数范例

```json
{
	"api_key": "e624ec5f5d8bcc5303dab6ed54c655e5",
	"pickup_info": {
		"has_liquid": false,
		"has_battery": true,
		"has_sensitive": false,
		"weight": 0,
		"pickup_time": "2019-07-11T19:07:01+00:00",
		"message": "Arrived at the EPC wharehouse",
		"recipient_name": "",
		"pickup_status": "ARRIVE_EPC_WH",
		"logistics_tracking_number": "VO0000013371VA",
		"status": 0,
		"oversize": false,
		"length": 1.1,
		"width": 2.1,
		"height": 1.2
	},
	"carrier_code": 4,
	"timestamp": "2019-07-11T19:14:22+00:00"
}
```


> curl请求

```shell
curl https://graphql-dev.shoppo.com/api/wise/logistics/stamp -H "Content-Type:application/json" -H "accesstoken: 6146211cb3c54a5999b78f4df1f88b63" -X POST --data '{"api_key":"e624ec5f5d8bcc5303dab6ed54c655e5","pickup_info":{"has_liquid":false,"has_battery":true,"has_sensitive":false,"weight":0,"pickup_time":"2019-07-11T19:07:01+00:00","message":"Arrived at the EPC wharehouse","recipient_name":"","pickup_status":"ARRIVE_EPC_WH","logistics_tracking_number":"VO0000013371VA","status":0},"carrier_code":4,"timestamp":"2019-07-11T19:14:22+00:00"}'
```


### 返回参数说明:

名称  |  二级名称  |   类型    | 必填   | 描述
---  |   ---  | --- | --- | ---
code |        | int | 是 | 错误编码
message |     | string | 是 | 消息
data |  | object    | 是 | 数组
     | tracking_id | string | 是 | 原单号   |



> 接收成功

```json
{
	"data": {
		"tracking_id": "VO0000013371VA"
	},
	"code": 0,
	"message": ""
}
```

### 异常信息

- **3501** 找不到原单号


> 异常

> 找不到原单号

```json
{
	"data": {
		"tracking_id": "VO0000013371VA"
	},
	"code": 3501,
	"message": "找不到包裹: VO0000013371VA"
}
```



## 合单出库

![](./images/wise3.2.png)

### `/api/wise/logistics/outbound`

- 传合并的小包原单号数组、重量、安检信息
- 请求方式: `POST`


### json请求参数说明


名称 | 类型 | 必填 | 描述
--- | --- | --- | ---
tracking_ids    | array   | 是 | 原单号列表 |
weight          | float   | 是 | 总重       |
has_liquid      | boolean | 是 | 是否带液体 |
has_battery     | boolean | 是 | 是否带电   |
has_sensitive   | boolean | 是 | 是否违禁   |
oversize        | boolean | 是 | 是否超尺寸 |
carrier_code    | string  | 是 | 渠道号    |


> 参数范例

```json
{
	"tracking_ids": ["KE11A091905091801", "KE11A091905088704"],
	"weight": 0.05,
	"has_liquid": false,
	"has_battery": false,
	"has_sensitive": false,
    "oversize": false,
    "carrier_code": "9903"
}
```

### 返回参数说明:

名称 | 二级名称 | 类型 | 必填 | 描述
--- | --- | --- | --- | ---
code | | int | 是 | 代码
message | | string | 是 | 消息
data | | object | 是 | 字典
     | logistics_order_code | string | 是 | 换单号       |
     | stamp_url            | string | 否 | 换单面单链接 |
     | label_size           | string | 是 | 面单尺寸     |
     | sku_stamp_url        | string | 否 | Sku面单链接  |
     | invoice_url          | string | 否 | 发票面单链接 |
     | invoice_size         | string | 否  | 发票尺寸         |


> 换单成功

```json
{
    "code": 0,
    "data": {
        "invoice_url": "http://soda-prod.oss-accelerate.aliyuncs.com/pdf/86a1fb111cdb4f5a9f8f710c2bae2936.pdf",
        "invoice_size": "10*15",
        "label_size": "10*15",
        "logistics_order_code": "shoppotest20191116002",
        "sku_stamp_url": "",
        "stamp_url": "http://soda-prod.oss-accelerate.aliyuncs.com/pdf/7bc6d42c10ce487bb2266f35d1aaf421.pdf"
    },
    "message": ""
}
```


### 异常信息

- **3501** 找不到包裹
- **3502** 小件已退货
- **3053** 小件合并错误
- **3054** 小件遗漏

> 异常

> 小件已退货

```json
{
	"code": 3502,
	"message": "BR02A021905100004已退货"
}
```


> 异常

> 小件合并错误, 传了其他订单的小件单号


```json
{
	"code": 3503,
	"message": "以下包裹不能一起合并: BR02A021905100004"
}
```


> 异常

> 小件合并错误, 该渠道的包裹不需要合并, 传了多个小件单号

```json
{
	"code": 3503,
	"message": "MALL91渠道不需要合并"
}
```


> 异常

> 小件遗漏

```json
{
	"code": 3504
    "message": "BR02A021905100004缺失"
}
```


## 获取下单数据

### `/api/wise/logistics/combine_order`

- 根据原单号获取下单数据
- 请求方式: `POST`

### json请求参数说明

名称 | 类型 | 必填 | 描述
--- | --- | --- | ---
tracking_id    | string   | 是 | 原单号 |

> 参数范例

```json
{
	"tracking_id": "BR02A021908019964"
}
```

> curl请求

```shell
curl http://api-dev.shoppo.com:14041/api/wise/logistics/combine_order -H "Content-Type:application/json" -H "accesstoken: 6146211cb3c54a5999b78f4df1f88b63" -X POST --data '{"tracking_id":"BR02A021908019964"}'
```

### 返回参数

> 调用成功

```json
{
	"code": 0,
	"data": {
		"merchant_transaction_id": "",
		"tracking_id": "BR02A021908019964",
		"combine_id": "190828145",
		"ready_to_ship": false,
		"goods_type": "1",
		"status": 1,
		"destination_country": "BR",
		"warehouse_code": "WSSZ001",
		"product": "5cac483bd09f6914c43cd5a9",
		"first_mile_carrier": "9903"
	},
	"message": ""
}
```


> 找不到包裹

```json
{
	"code": 3501,
	"message": "找不到包裹: BR02A0219082833828"
}
```


## 错误码说明

代码  | 描述
--- | --- 
3501 | 找不到原单号
3502 | 小件已退货
3053 | 小件合并错误
3054 | 小件遗漏
