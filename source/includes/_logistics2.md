# 物流V2


## 小包入库


![](./images/wise2.1.png)


###  `/api/wise/logistics/inbound`

- 传小包原单号数组、重量、安检信息
- 请求方式: `POST`


### json请求参数说明

名称 | 二级名称 | 类型 | 必填 | 描述
--- | ---- | --- | --- | ----
data |     | array | 是 | 数组
     | trackingId   | string  | 是 | 原单号    |
     | weight          | float   | 是 | 重量       |
     | hasBattery      | boolean | 是 | 是否带电   |
     | hasLiquid       | boolean | 是 | 是否带液体 |
     | hasSensitive    | boolean | 是 | 是否违禁   |
     | timeInbound     | string  | 是 | 入库时间   |



> 参数范例

```json
{
	"data": [{
			"trackingId": "IN19A041905106124",
			"weight": 0.047,
			"hasBattery": false,
			"hasLiquid": false,
			"hasSensitive": false,
			"timeInbound": "2019-05-09 13:01:07"
		},
		{
			"trackingId": "BR02A021905100004",
			"weight": 0.047,
			"hasBattery": true,
			"hasLiquid": false,
			"hasSensitive": false,
			"timeInbound": "2019-05-09 16:12:33"
		}
	]                                           
}
```


> curl请求

```shell
curl https://graphql-dev.shoppo.com/api/wise/logistics/stamp -H "Content-Type:application/json" -H "accesstoken: 6146211cb3c54a5999b78f4df1f88b63" -X POST --data '{"data":[{"trackingId":"IN19A041905106124","weight":0.047,"hasBattery":false,"hasLiquid":false,"hasSensitive":false,"timeInbound":"2019-05-09 13:01:07"},{"trackingId":"BR02A021905100004","weight":0.047,"hasBattery":true,"hasLiquid":false,"hasSensitive":false,"timeInbound":"2019-05-09 16:12:33"}]}'
```


### 返回参数说明:

名称  |  二级名称  |   类型    | 必填   | 描述
---  |   ---  | --- | --- | ---
errorCode |  |  int  | 是 | 代码
errorMessage |  |  string  | 是 | 消息
data |  | array    | 是 | 数组
     | trackingId | string | 是 | 原单号 |
     | action     | int    | 是 | 指令   |
     | combineId  | string | 是  | 合并标识    |


> 目前就返回三种指令
> - 接收成功
> - 等待合包
> - 退回商户


> 接收成功

```json
{
	"errorCode": 0,
	"errorMessage": "",
	"data": [{
		"action": 1,
		"trackingId": "IN19A041905106124"
	}, {
		"action": 1,
		"trackingId": "BR02A021905100004"
	}]
}
```


> 等待合包

```json
{
	"errorCode": 0,
	"errorMessage": "BR02A021905100004等待合包",
	"data": [{
		"action": 1,
		"trackingId": "IN19A041905106124"
	}, {
		"action": 2,
		"trackingId": "BR02A021905100004"
	}]
}
```


> 退回商户

```json
{
	"errorCode": 0,
	"errorMessage": "退回IN19A041905106124, 退回BR02A021905100004",
	"data": [{
		"action": 4,
		"trackingId": "IN19A041905106124"
	}, {
		"action": 4,
		"trackingId": "BR02A021905100004"
	}]
}
```


### 异常信息

- **3501** 找不到原单号


> 异常

> 找不到原单号

```json
{
	"errorCode": 3501,
	"errorMessage": "找不到BR02A021905100004"
}
```



## 指令说明

代码 | 描述
--- | ---
1   | 接收成功/换单成功
2   | 等待合包
3   | 部分发货
4   | 退回商户



## 合单出库

![](./images/wise3.2.png)

### `/api/wise/logistics/outbound`

- 传合并的小包原单号数组、重量、安检信息
- 请求方式: `POST`


### json请求参数说明


名称 | 二级名称 | 类型 | 必填 | 描述
--- | --- | --- | --- | ---
data | | object | 是 | 字典
     | trackingIds   | array   | 是 | 原单号列表 |
     | weight        | float   | 是 | 总重       |
     | hasLiquid     | boolean | 是 | 是否带液体 |
     | hasBattery    | boolean | 是 | 是否带电   |
     | hasSensitive  | boolean | 是 | 是否违禁   |


> 参数范例

```json
{
	"data": {
		"trackingIds": ["KE11A091905091801", "KE11A091905088704"],
		"weight": 0.05,
		"hasLiquid": false,
		"hasBattery": false,
		"hasSensitive": false
	}
}
```

### 返回参数说明:

名称 | 二级名称 | 类型 | 必填 | 描述
--- | --- | --- | --- | ---
code | | int | 是 | 代码
message | | string | 是 | 消息
data | | object | 是 | 字典
     | action               | int    | 是 | 指令             |
     | logisticsOrderCode   | string | 是 | 换单号           |
     | cancelledTrackingIds | string | 否 | 取消的原单号列表 |
     | missingTrackingIds   | array  | 否 | 漏掉的原单号列表 |
     | stampUrl             | string | 否 | 换单面单链接     |
     | skuStampUrl          | string | 否  | Sku面单链接          |


> 换单成功

```json
{
	"errorCode": 0,
	"errorMessage": "换单",
	"data": {
		"action": 1,
		"stampUrl": "https://cdn.shoppo.com/temporary_uploaded_files/948d3fb59a0c4cbaa88b6e12b3c97a91.pdf",
        "skuStampUrl": "https://cdn.shoppo.com/temporary_uploaded_files/948d3fb59a0c4cbaa88b6e12b3c97a91.pdf",
		"logisticsOrderCode": "KE0480397"
	}
}
```

> 部分发货

```json
{
	"errorCode": 0,
	"errorMessage": "KE11A091905088704退货",
	"data": {
		"action": 3,
		"cancelledTrackingIds": ["KE11A091905088704"],
		"stampUrl": "https://cdn.shoppo.com/temporary_uploaded_files/948d3fb59a0c4cbaa88b6e12b3c97a91.pdf"
	}
}
```

> 退回商户

```json
{
	"errorCode": 0,
	"errorMessage": "KE11A091905091801退货, KE11A091905088704退货",
	"data": {
		"action": 4
	}
}
```


### 异常信息

- **3501** 找不到原单号
- **3502** 小件已退货
- **3053** 小件合并错误
- **3054** 小件遗漏

> 异常

> 小件已退货

```json
{
	"errorCode": 3502,
	"errorMessage": "BR02A021905100004已退货"
}
```


> 异常

> 小件合并错误


```json
{
	"errorCode": 3503,
	"errorMessage": "BR02A021905100004不应合并"
}
```

> 异常

> 小件遗漏

```json
{
	"errorCode": 35034
    "errorMessage": "BR02A021905100004缺失"
}
```

## 错误码说明

代码  | 描述
--- | --- 
3501 | 找不到原单号
3502 | 小件已退货
3053 | 小件合并错误
3054 | 小件遗漏




