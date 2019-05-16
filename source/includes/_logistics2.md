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
     | tracking_id     | string  | 是 | 原单号    |
     | weight          | float   | 是 | 重量       |
     | has_battery     | boolean | 是 | 是否带电   |
     | has_liquid      | boolean | 是 | 是否带液体 |
     | has_sensitive   | boolean | 是 | 是否违禁   |
     | time_inbound    | string  | 是 | 入库时间   |



> 参数范例

```json
{
	"data": [{
			"tracking_id": "IN19A041905106124",
			"weight": 0.047,
			"has_battery": false,
			"has_liquid": false,
			"has_sensitive": false,
			"time_inbound": "2019-05-09 13:01:07"
		},
		{
			"tracking_id": "BR02A021905100004",
			"weight": 0.047,
			"has_battery": true,
			"has_liquid": false,
			"has_sensitive": false,
			"time_inbound": "2019-05-09 16:12:33"
		}
	]
}
```


> curl请求

```shell
curl https://graphql-dev.shoppo.com/api/wise/logistics/stamp -H "Content-Type:application/json" -H "accesstoken: 6146211cb3c54a5999b78f4df1f88b63" -X POST --data '{"data":[{"tracking_id":"IN19A041905106124","weight":0.047,"has_battery":false,"has_liquid":false,"has_sensitive":false,"time_inbound":"2019-05-09 13:01:07"},{"tracking_id":"BR02A021905100004","weight":0.047,"has_battery":true,"has_liquid":false,"has_sensitive":false,"time_inbound":"2019-05-09 16:12:33"}]}'
```


### 返回参数说明:

名称  |  二级名称  |   类型    | 必填   | 描述
---  |   ---  | --- | --- | ---
code |  |  int  | 是 | 代码
message |  |  string  | 是 | 消息
data |  | array    | 是 | 数组
     | tracking_id               | string | 是 | 运单号         |
     | action                    | int    | 是 | 指令           |


> 目前就返回三种指令
> - 接收成功
> - 等待合包
> - 退回商户


> 接收成功

```json
{
	"code": 0,
	"message": "",
	"data": [{
		"action": 1,
		"tracking_id": "IN19A041905106124"
	}, {
		"action": 1,
		"tracking_id": "BR02A021905100004"
	}]
}
```


> 等待合包

```json
{
	"code": 0,
	"message": "BR02A021905100004等待合包",
	"data": [{
		"action": 1,
		"tracking_id": "IN19A041905106124"
	}, {
		"action": 2,
		"tracking_id": "BR02A021905100004"
	}]
}
```


> 退回商户

```json
{
	"code": 0,
	"message": "IN19A041905106124退回, BR02A021905100004退回",
	"data": [{
		"action": 4,
		"tracking_id": "IN19A041905106124"
	}, {
		"action": 4,
		"tracking_id": "BR02A021905100004"
	}]
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
     | tracking_ids  | array   | 是 | 原单号列表 |
     | weight        | float   | 是 | 总重       |
     | has_liquid    | boolean | 是 | 是否带液体 |
     | has_battery   | boolean | 是 | 是否带电   |
     | has_sensitive | boolean | 是 | 是否违禁   |


> 参数范例

```json
{
	"data": {
		"tracking_ids": ["KE11A091905091801", "KE11A091905088704"],
		"weight": 0.05,
		"has_liquid": false,
		"has_battery": false,
		"has_sensitive": false
	}
}
```

### 返回参数说明:

名称 | 二级名称 | 类型 | 必填 | 描述
--- | --- | --- | --- | ---
code | | int | 是 | 代码
message | | string | 是 | 消息
data | | object | 是 | 字典
     | action                 | int    | 是 | 指令             |
     | logistics_order_code   | string | 是 | 换单号           |
     | cancelled_tracking_ids | string | 否 | 取消的原单号列表 |
     | missing_tracking_ids   | array  | 否 | 漏掉的原单号列表 |
     | stamp_url              | string | 否  | 换单面单链接           |


> 换单成功

```json
{
	"code": 0,
	"message": "换单",
	"data": {
		"action": 1,
		"stamp_url": "https://cdn.shoppo.com/temporary_uploaded_files/948d3fb59a0c4cbaa88b6e12b3c97a91.pdf",
		"logistics_order_code": "KE0480397"
	}
}
```

> 部分发货

```json
{
	"code": 0,
	"message": "KE11A091905088704退货",
	"data": {
		"action": 3,
		"cancelled_tracking_ids": ["KE11A091905088704"],
		"stamp_url": "https://cdn.shoppo.com/temporary_uploaded_files/948d3fb59a0c4cbaa88b6e12b3c97a91.pdf"
	}
}
```

> 退回商户

```json
{
	"code": 0,
	"message": "KE11A091905091801退货, KE11A091905088704退货",
	"data": {
		"action": 4,
		"cancelled_tracking_ids": ["KE11A091905091801", "KE11A091905088704"]
	}
}
```
