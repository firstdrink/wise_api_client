# 物流


## 流程图

![](./images/wise.png)


## 下载物流面单


### `/api/wise/logistics/stamp`

- 传商品信息，称重数据返回物流面单
- 请求方式: `POST`


> curl请求

```shell
$ curl https://graphql-dev.shoppo.com/api/wise/logistics/stamp -H "Content-Type:application/json" -H "accesstoken: 6146211cb3c54a5999b78f4df1f88b63" -X POST --data '{"order_items":[{"tracking_number":"Z6y2yDEZpoaFa6","weight":2.2},{"tracking_number":"aKgRgDZ6vnvug1","weight":1.1}],"tracking_number":"NKozBgz8gvqCoY", "has_battery": true}'

```

### json请求参数说明:

名称 | 二级名称 | 类型 | 必填 | 描述
--- | ---- | --- | --- | ---
tracking_number | | string | 是 | 合单物流单号，也就是logistics_order_code
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
    | status_number | int    | 是 | 状态码 1: 小件揽收, 2: 小件入仓, 3: 合单出仓, 4: 小件退货, 5: 退货签收 |
    | status_desc   | string | 否 | 状态说明                                                               |
    | date          | string | 是 | 日期时间， 如 2016-08-22 00:00:00                                      |
    | remark        | string | 否 | 说明                                                                   |
    | location      | string | 是 | 位置                                                                   |
    | weight        | float  | 否  |  重量， 单位Kg                                                              |
    | has_battery   | boolean | 否  | 是否带电                                                                   |



## 获取物流订单

### `/api/wise/logistics/order`

- `Wise` 调用 `Shoppo` 接口，获取物流订单信息
- 请求方式: `POST`



> curl请求

```shell

$ curl https://graphql-dev.shoppo.com/api/wise/logistics/order -H "Content-Type:application/json" -H "accesstoken: <Your access token>" -X POST --data '{"tracking_number":"KE0231645SH01"}'

```

### json请求参数说明

名称 | 二级名称 | 类型 | 必填 | 描述
--- | ---- | --- | --- | ---
tracking_number | | string | 是 | 小包物流单号


> json结果

> status code: 200

```json
{
	"api_key": "8D075F4EF0B04891228233840D8BED9F",
	"logistics_order_code": "KE0231645",
	"tracking_id": "KE0231645SH01",
	"order_time": "2019-01-25T05:00:28",
	"timestamp": "2019-01-25T09:41:26.953780",
	"carrier_code": 9901,
	"carry_type": 1,
	"pick_type": 1,
	"otype": "shoppo_3-0",
	"sname": "Shoppo",
	"shoppo_sname": "Shoppo",
	"shoppo_service_type": "Shoppo_Combine",
	"user_custom_description": "",
	"paid_with_shoppo": false,
	"payment_account": null,
	"sys_user": "0nl8q48x20MiX",
	"parcel": {
		"description_en": "Men Accessory Classic Ties Striped Plaid Necktie",
		"description_local": "",
		"weight": 1.0,
		"weight_unit": "kg",
		"declare_value": 4.29,
		"price_unit": "dollar",
		"price_currency": "usd",
		"has_battery": false,
		"is_plastic": true,
		"product_list": [{
			"description_en": "Men Accessory Classic Ties Striped Plaid Necktie",
			"description_local": "Men Accessory Classic Ties Striped Plaid Necktie",
			"origin_country": "中国",
			"origin_country_code": "CHN",
			"value": 4.29,
			"weight": 1.0,
			"quantity": 1,
			"has_battery": false,
			"sku": "z0G3GM2gAkVUkl"
		}]
	},
	"sender": {
		"company": "Luckeve",
		"mobile": "",
		"phone": "15919793526",
		"country_code": "CHN",
		"zipcode": "518116",
		"address_en": {
			"name": "chen si",
			"country": "China",
			"province": "Guangdong",
			"city": "Shenzhen",
			"street_address1": "8/F,Building No.2,Qianlong logistics Park, No.3 ping'an Road.Pinghu Town"
		}
	},
	"receiver": {
		"company": "Kilimall",
		"mobile": "13751353958",
		"phone": "",
		"country_code": "CHN",
		"address_en": {
			"name": "Kilimall",
			"country": "China",
			"province": "Guangzhou",
			"city": "Dongguan",
			"street_address1": "Dao jiao town, Jiu qu village, Da zhong Road, Ji Yuntong South China Distribution Center"
		},
		"address_local": {
			"name": "Kilimall",
			"country": "中国",
			"province": "广州",
			"city": "东莞",
			"street_address1": "道滘镇九曲村大众路集运通华南配送中心"
		}
	},
	"return_info": {
		"return_action_in_country": 3,
		"return_action_out_country": 0,
		"return_address_in_country": {
			"company": "Kilimall",
			"phone": "",
			"mobile": "13751353958",
			"country_code": "CHN",
			"address_en": {
				"name": "Kilimall",
				"country": "China",
				"province": "Guangzhou",
				"city": "Dongguan",
				"street_address1": "Dao jiao town, Jiu qu village, Da zhong Road, Ji Yuntong South China Distribution Center"
			},
			"address_local": {
				"name": "Kilimall",
				"country": "中国",
				"province": "广州",
				"city": "东莞",
				"street_address1": "道滘镇九曲村大众路集运通华南配送中心"
			}
		}
	}
}

```
