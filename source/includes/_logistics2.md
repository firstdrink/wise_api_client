# 物流V2


## 小包入库


![](./images/wise2.png)


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
     | time_expired              | string | 是 | 超时时间       |
     | logistics_order_code      | string | 否 | 换单号         |
     | stamp_url                 | string | 否 | 换单链接       |
     | combined_tracking_numbers | array  | 否 | 合并原单号列表 |



> 合包

```json
{
	"code": 0,
	"message": "KE11A091905072504, KE11A091905083705共2件合并",
	"data": {
		"action": 1,
		"combined_tracking_ids": ["KE11A091905072504", "KE11A091905083705"]
	}
}
```


> 换单

```json
{
	"code": 0,
	"message": "换单",
	"data": {
		"action": 2,
		"stamp_url": "https://cdn.shoppo.com/temporary_uploaded_files/948d3fb59a0c4cbaa88b6e12b3c97a91.pdf",
		"logistics_order_code": "KE0478549"
	}
}
```


> 出库

```json
{
	"code": 0,
	"message": "出库",
	"data": {
		"action": 3
	}
}
```

> 等待合包

```json
{
	"code": 0,
	"message": "KE11A091905091801, KE11A091905083705等待入库",
	"data": {
		"action": 4,
		"time_expired": "2019-05-10 12:00:00",
		"combined_tracking_ids": ["KE11A091905091801", "KE11A091905083705", "KE11A091905073903"]
	}
}
```


> 退回商户

```json
{
	"code": 0,
	"message": "订单已取消",
	"data": {
		"action": 5
	}
}
```


## 指令说明

代码 | 描述
--- | ---
1   | 合包
2   | 换单
3   | 出库
4   | 等待合包
5   | 退回商户


## 合单出库

![](./images/wise3.1.png)

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
     | action               | int    | 是 | 指令     |
     | logistics_order_code | string | 否 | 换单号   |
     | stamp_url            | string | 否 | 面单链接 |
     | combined_tracking_ids | array  | 否  | 合并原单号列表  |


> 换单

```json
{
	"code": 0,
	"message": "换单",
	"data": {
		"action": 2,
		"stamp_url": "https://cdn.shoppo.com/temporary_uploaded_files/948d3fb59a0c4cbaa88b6e12b3c97a91.pdf",
		"logistics_order_code": "KE0480397"
	}
}
```

> 合包(缺失某个包裹, 该包裹已经入库)

```json
{
	"code": 0,
	"message": "KE11A091905091801, KE11A091905088704, KE11A091905083705共计3件合并。 KE11A091905083705缺失",
	"data": {
		"action": 1
	}
}
```

> 等待合包

```json
{
	"code": 0,
	"message": "KE11A091905091801, KE11A091905088704, KE11A091905083705共计3件合并。 KE11A091905083705等待入库",
	"data": {
		"action": 4
	}
}
```
